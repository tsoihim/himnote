## **개요**
Ironic을 Production Level로 테스트해보기 위한 환경은 쉽게 구축하기가 힘듬 (실제 베어메탈 등 필요)
이에, 클라우드 개발자들이 KVM과 VirtualBMC를 통해 실제 Deploy 수행할 베어메탈 서버가 없어도 Ironic 기능을 테스트해볼 수 있도록 환경을 구성하는 방법에 대해 서술

## **환경 구성**
### 준비물
- 서버 한대
    - 같은 LAN(VLAN)에 속한 네트워크 인터페이스를 두개 가져야함
- 많은 시간
- 약간의 지능
### 구성도
- 구성요소
    - **KVM Host 서버** : OpenStack, Target VM 인스턴스들을 호스팅할 서버
        - **OpenStack VM** : 실제 OpenStack 서비스를 설치할 KVM 인스턴스
        - **Target VM** : Ironic 통해 Deploy할 가상 베어메탈 KVM 인스턴스
### 목표 시나리오
위 구성이 완료되었다는 것을 가정한 후의 시나리오
1. OpenStack에 베어메탈 노드를 등록
2. Image, Port 등 베어메탈 Deploy에 필요한 사전 설정 수행
3. 베어메탈 Deploy 및 동작 확인

## **구성 절차**
아래의 순서로 작업을 수행하여 위 구성도에 상응하는 환경을 만들 수 있음
### KVM 인스턴스 2개 생성
아래와 같이 KVM 호스트 서버에 OpenStack VM(caracal-cinder)과 Target VM(bare1) 인스턴스를 생성함
- 두 VM 모두 Rocky9을 설치
    - Target VM은 Ironic Deploy가 완료되면 Cirros로 운영체제가 바뀌게 될 것임
- 네트워크 모드는 Bridge 모드를 사용
    - OpenStack VM은 인터페이스 및 IP를 모두 잡아줌
        - OpenStack 설치를 위해 인터페이스를 2개 잡아줌
        - 한 인터페이스는 IP 설정까지 완료
    - Target VM의 경우에는 베어메탈 Deploy의 타겟이므로 인터페이스만 잡아준 후 IP 설정은 별도로 해주지 않았음
```
root@sdn197:~# virsh list
 Id   Name             State
--------------------------------
 17   caracal-cinder   running
 21   bare1            running
```

### VirtualBMC 설치
**1. IPMI로 구성하는 경우**
KVM 호스트 서버에서 아래와 같은 절차로 VirtualBMC 설치 및 BMC 추가
- [Ubuntu 22.04 LTS : KVM : Use VirtualBMC : Server World](https://www.server-world.info/en/note?os=Ubuntu_22.04&p=kvm&f=14)
```
# 설치
root@dlp:~# apt -y install python3-pip python3-venv ipmitool
root@dlp:~# python3 -m venv --system-site-packages /opt/virtualbmc
root@dlp:~# /opt/virtualbmc/bin/pip3 install virtualbmc
# 설치 후 Linux 서비스 등록/활성화
root@dlp:~# vi /usr/lib/systemd/system/virtualbmc.service
# create new
[Unit]
Description=Virtual BMC Service
After=network.target libvirtd.service
[Service]
Type=simple
ExecStart=/opt/virtualbmc/bin/vbmcd --foreground
ExecStop=/bin/kill -HUP $MAINPID
User=root
Group=root
[Install]
WantedBy=multi-user.target
root@dlp:~# systemctl daemon-reload
root@dlp:~# systemctl enable --now virtualbmc.service
# bare1 VM의 BMC 생성 및 시작 (기본 IPMI 포트인 623 사용)
root@dlp:~# /opt/virtualbmc/bin/vbmc add bare1 --username vbmcadmin --password adminpassword
root@dlp:~# /opt/virtualbmc/bin/vbmc start bare1
root@dlp:~# /opt/virtualbmc/bin/vbmc list
+-------------+---------+---------+------+
| Domain name | Status  | Address | Port |
+-------------+---------+---------+------+
| bare1       | running | ::      |  623 |
+-------------+---------+---------+------+
```
설치 후 다른 서버에서 ipmitool로 10.0.112.197 IP로 서버에 쿼리를 날려 정상 동작하는지 확인 가능
```
root@sdn197:~# ipmitool -I lanplus -H 10.0.112.197 -U vbmcadmin -P adminpassword power status
Chassis Power is on
```

**2. Redfish로 구성하는 경우**
KVM 환경에서 redfish를 emulate해주는 sushy-tools를 설치
- [Sushy Tools - Redfish Libvirt.md](https://gist.github.com/williamcaban/e5d02b3b7a93b497459c94446105872c)
```
root@sdn197:~# python3 -m venv /opt/sushy-tools
root@sdn197:~# . /opt/sushy-tools/bin/activate
root@sdn197:~# pip3 install sushy-tools
root@sdn197:~# apt-get install libvirt-dev
root@sdn197:~# pip3 install libvirt-python
root@sdn197:~# vi /opt/sushy-tools/sushy-emulator.conf # 아래의 링크를 보고 sushy-tools 설정 파일 작성
# <https://github.com/openstack/sushy-tools/blob/master/doc/source/admin/emulator.conf>
# 주요 설정
SUSHY_EMULATOR_LISTEN_IP = u'10.0.112.197'
SUSHY_EMULATOR_LISTEN_PORT = 443
SUSHY_EMULATOR_OS_VMEDIA_IMAGE_FILE_UPLOAD = True
SUSHY_EMULATOR_LIBVIRT_URI = u'qemu:///system'
SUSHY_EMULATOR_IGNORE_BOOT_DEVICE = True
SUSHY_EMULATOR_BOOT_LOADER_MAP = {
   'UEFI': {
       'x86_64': u'/usr/share/OVMF/OVMF_CODE.fd',
       'aarch64': u'/usr/share/AAVMF/AAVMF_CODE.fd'
   },
   'Legacy': {
       'x86_64': None,
       'aarch64': None
   }
}
SUSHY_EMULATOR_ALLOWED_INSTANCES = [
   "9833f7b7-48fa-4936-9890-2ed15ba875f8" # 타겟 VM의 domuuid
]
root@sdn197:~# vi /etc/systemd/system/sushy-tools.service # 설치 후 Linux 서비스 등록/활성화
[Unit]
Description=Sushy Tools (Redfish Emulator for Libvirt)
After=network.target syslog.target
[Service]
Type=simple
TimeoutStartSec=5m
#User=<your-user>
WorkingDirectory=/opt/sushy-tools
ExecStart=/opt/sushy-tools/bin/python3 /opt/sushy-tools/bin/sushy-emulator --config /opt/sushy-tools/sushy-emulator.conf
Restart=always
[Install]
WantedBy=multi-user.target
root@sdn197:~# systemctl daemon-reload
root@sdn197:~# systemctl enable --now sushy-tools
root@sdn197:~# systemctl start sushy-tools
```
설치 후 curl로 서버에 쿼리를 날려 정상 동작하는지 확인 가능
```
root@sdn197:~# curl <http://10.0.112.197:443/redfish/v1/Systems>
{
    "@odata.type": "#ComputerSystemCollection.ComputerSystemCollection",
    "Name": "Computer System Collection",
    ...
    ...
}
```

### OpenStack 설치
Ironic을 포함한 OpenStack 설치는 아래 문서 참고
- [https://docs.openstack.org/kolla-ansible/latest/reference/bare-metal/ironic-guide.html](https://docs.openstack.org/kolla-ansible/latest/reference/bare-metal/ironic-guide.html
- kolla-ansible로 openstack, ironic 설치 시 주의사항
    - `ironic_dnsmasq_dhcp_ranges`설정 시 같은 LAN에 속하도록 대역 설정
    - `neutron_plugin_agent`는 openvswitch로 설정
    - KVM 위에 OpenStack을 설치하므로 `nova_compute_virt_type`은 qemu로 설정

### Ironic 설정
OpenStack 설치 완료 후 Ironic 사용을 위해 kernel 및 램디스크 이미지를 Glance에 추가 필요 (AKI/ARI)
여기까지 작업을 완료하였다면, 이후로는 자유롭게 Target VM을 새로 추가하거나 Power On/Off 등의 API를 호출하여 Ironic의 기능을 테스트해볼 수 있음

### 참고 문서
- [Ubuntu 22.04 LTS : KVM : Use VirtualBMC : Server World](https://www.server-world.info/en/note?os=Ubuntu_22.04&p=kvm&f=14)