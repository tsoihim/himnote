Rocky linux 에 kolla ansible 을 이용하여 openstack 설치하는 방법에 대해 정리
LVM을 이용하는 Cinder 를 추가, Octaiva를 추가

## **제약**
kolla-ansible 로 생성하는 openstack 은 nic 이 최소 2개 있는 환경에서 설치가 가능하다.

## **사전 작업**
### **LVM 설정**
아래와 같이 OpenStack 설치를 진행할 타겟 KVM 인스턴스에 sdb 디스크를 별도로 추가해주고 볼륨 그룹을 생성해준다.
```
[root@localhost ~]# lsblk -f
NAME        FSTYPE      FSVER    LABEL UUID                                   FSAVAIL FSUSE% MOUNTPOINTS
sda
├─sda1      xfs                        519316f3-a26d-4f4a-9014-89f6e9b82814    641.8M    33% /boot
└─sda2      LVM2_member LVM2 001       2CRuTP-95ch-cqHo-nTL8-3Md9-Ftp9-6UaEbn
  ├─rl-root xfs                        6dc4bde4-5b0a-4c4d-bf77-dfab3ed6bc2d     67.4G     4% /
  ├─rl-swap swap        1              b582e5e7-e3cf-4388-bcda-8e589a8eca2e                  [SWAP]
  └─rl-home xfs                        d1ec8c4d-ef3a-4358-a29d-8aa7d41645d5     43.8G     1% /home
sdb
sr0
[root@localhost ~]# pvcreate /dev/sdb
  Physical volume "/dev/sdb" successfully created.
[root@localhost ~]# vgcreate cinder-volumes /dev/sdb
  Volume group "cinder-volumes" successfully created
```

### **Rocky Linux**
Rokcy linux는 버젼과 상관 없어 보인다. 가능한 최신 버젼을 이용하는 것이 좋아 보인다.
다음처럼 package 버젼을 최신화하고, 필요한 패키지들을 설치한다.
```
dnf update -y
dnf install git python3-devel libffi-devel gcc openssl-devel python3-libselinux
```
설치를 진행하려면, `ntp` 가 필요한데, Rocky linux 는 `chrony` 가 설치되어 있으나 disable 되어 있다. 이를 실행해 준다.
```
systemctl enable chronyd
systemctl start chronyd
```
`nmtui` 로 호스트 이름과 ip 등을 확인하고 필요에 따라 변경해준다. (host 별로 이름이 다 달라야 한다. 동일한 이름을 이용하면 `ansible` 에서 에러가 나니 주의하자.)

### **공통 작업**
python env 를 이용해 가상 환경에서 작업한다. (여기서는 kolla 라는 이름으로 가상 환경을 만들었다.)
```
python3 -m venv kolla
source kolla/bin/activate
```
가상 환경 안에 ansible 과 kolla-ansible 을 설치한다.
- kolla-ansible `stable/2024.1`의 최근 버전인 18.4.0 사용 시 문제가 있어 명시적으로 18.3.0을 설치한다.
    - rabbitmq user 있는 지 체크하는 데에서 문제가 발생했다.
```
pip install -U pip
pip install 'ansible-core>=2.14,<2.16'
pip install git+https://opendev.org/openstack/kolla-ansible@18.3.0
```
이제 kolla 를 위한 디렉토리와 기초 설정 파일들을 복사한다.
```
mkdir /etc/kolla
cp kolla/share/kolla-ansible/etc_examples/kolla/* /etc/kolla/
cp kolla/share/kolla-ansible/ansible/inventory/* /etc/kolla/
```

### **ansible 사전 작업**
ansible 작업을 위해, ssh 접속용 key를 만들어 배포한다.
```
ssh-keygen
ssh-copy-id root@[장비 ip 들]
```
위와 같이 ssh-keygen 으로 ssh용 key를 생성하고 (편의를 위해 암호는 생략한다.), 만들어진 key를 접속하기 위한 장비들 (자신 포함)에 복사하는 작업을 해 둔다. 해당 장비의 hostkey 를 저장하는 역할과 암호 없이 접속 가능하게 설정하는 두 가지 작업을 해 둘 수 있다.

## **설치**
### **설정**
`/etc/kolla/global.yml` 을 편집한다. 대부분의 경우 기본값을 이용해도 되나, 최소한의 설정을 위해 다음 부분은 수정이 필요하다.
```
workaround_ansible_issue_8743: yes
kolla_internal_vip_address: "10.0.116.240"
network_interface: "ens3"
neutron_external_interface: "ens8"
neutron_plugin_agent: "openvswitch" # openvswitch로 바꾸어줌!!!
nova_compute_virt_type: "qemu"
enable_cinder: "yes"
enable_cinder_backup: "no"
enable_cinder_backend_lvm: "yes"
# 이하 설정이 중요
enable_octavia: "yes"
octavia_certs_country: US
octavia_certs_state: Oregon
octavia_certs_organization: OpenStack
octavia_certs_organizational_unit: Octavia
enable_redis : "yes"
octavia_network_type: "tenant" # 중요
```
- `octavia` 이외의 설정은 [Kolla-Ansible로 Openstack 설치 (LVM + Cinder)](https://www.notion.so/Kolla-Ansible-Openstack-LVM-Cinder-1c79c0a96a9d8079b84ded1c531c7e8c?pvs=21) 참고
- octavia_newtork_type을 tenant로 지정하면, 개발 레벨에서 사용할 수 있는 octavia tenant network 설정을 사용할 수 있음
    - 프로덕션 레벨에서는 사용하면 안되나, 테스트 용도로 설정 가능한 네트워크 타입임

### **암호 생성**
openstack 에서 사용할 password 들을 지정해 주어야 한다.
```
kolla-genpwd
```
명령을 입력하면, 자동으로 각종 컴포넌트에서 사용할 암호를 자동생성해서 `/etc/kolla/passwords.yml` 에 넣어준다.
이를 바로 이용해도 상관 없지만, 추후 편의를 위해 자주 사용하는 암호는 쉬운 것으로 바꾸는 것을 추천한다.
- database_password : db 접속용 암호
- keystone_admin_password : 설치 후, horizon 에서 접속할 때 사용하는 암호

### Octavia용 인증서 생성
```
kolla-ansible octavia-certificates
```

### **사전 검사 및 설치**
다음 처럼 사전 작업 및 설치 작업을 진행할 수 있다.
작업 전엔 다음과 같이 dependecy가 있는 package 설치가 필요하다. (docker 등 설정에 따라 필요한 package들이 자동으로 설치된다. 이 작업을 하지 않으면 여러 곳에서 에러가 날 수 있으니 주의)
```
kolla-ansible install-deps
```
순서대로 오류를 확인하면서 진행하면 된다.
```
kolla-ansible -i /etc/kolla/all-in-one bootstrap-servers
kolla-ansible -i /etc/kolla/all-in-one prechecks
kolla-ansible -i /etc/kolla/all-in-one pull
kolla-ansible -i /etc/kolla/all-in-one deploy
```
- 뭐가 문제인지 모르겠지만, bootstrap 작업 도중 dbus-run-session 관련 에러가 발생하는 경우가 있었다. dbus 관련 패키지들을 추가해 주면 해결이 가능한 것 같다. (정확히 뭐가 필요한진 잘 모르겠다. 마지막 dbus-daemon 은 필수인 듯)`dnf install python3-dbus dbus-devel dbus-python dbus-glib-devel dbus-tools dbus-daemon`
- fluentd 작업 도중 에러가 나는 경우가 있다. host쪽에 `/var/log/journal` 디렉토리를 만들어주면 해결 되는 것 같다. (Rocky Linux는 journal 위치가 /run/log/journal 이라 발생하는 것 같다.) `mkdir /var/log/journal`
- deploy 단계에서, dhcp-client가 필요할 수 있음`dnf -y install dhcp-client`
- deploy 단계에서, 사용할 interface들에 autoconnect를 yes로 변경해주어야 할 수 있음`nmcli connection modify "ens3" autoconnect yes nmcli connection modify "ens8" autoconnect yes`
작업이 완료되면, 다음 작업으로 관리를 위한 패키지들을 설치하고 관련 환경설정을 할 수 있다.
```
kolla-ansible post-deploy
pip install python-openstackclient -c <https://releases.openstack.org/constraints/upper/2024.1>
```

여기까지 설치가 완료되었다.

### Amphora image 등록
기본적인 flavor, lb-mgmt-subnet 등은 kolla가 자동으로 잡아줄 수 있지만, amphora 이미지의 경우 직접 빌드 및 등록이 필요하다.
아래와 같은 절차를 수행하여 이미지를 등록할 수 있다.
```
<https://github.com/osism/openstack-octavia-amphora-image?tab=readme-ov-file>
# 위 주소에서 적절한 qcow2 파일을 다운로드 받은 후 등록
. /etc/kolla/octavia-openrc.sh
openstack image create amphora-x64-haproxy.qcow2 --container-format bare --disk-format qcow2 --private --tag amphora --file amphora-x64-haproxy.qcow2 --property hw_architecture='x86_64' --property hw_rng_model=virtio
```

### **기본 설정 (optional)**
설치가 완료된 후, 사용 편의를 위한 기본 설정을 해 줄 수 있다.
기본적인 network 설치, flavor 설치, key 추가, 테스트를 위한 작은 vm인 cirros 이미지 등록 등 openstack 설치 후 초기 설정을 도와주는 작업이다.
```
./kolla/share/kolla-ansible/init-runonce
```
이 작업은 위의 openstack client 가 설치되고, post-deploy 가 설치된 후에 가능하다.
이제 기본 설정까지 마쳤으면 사용에 대한 준비가 완료되었으니 바로 이용할 수 있다.

### 특이사항
설치가 완료되면 lb-mgmt-subnet인 10.1.0.0/24에 상응하는 인터페이스가 OpenStack 설치한 서버에 생성되고, IP까지 할당받은 것을 확인할 수 있음
```
9: o-hm0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether fa:16:3e:45:b0:2b brd ff:ff:ff:ff:ff:ff
    inet 10.1.0.165/24 brd 10.1.0.255 scope global dynamic o-hm0
       valid_lft 83774sec preferred_lft 83774sec
    inet6 fe80::f816:3eff:fe45:b02b/64 scope link
       valid_lft forever preferred_lft forever
```

### 참고 문서
- [Cinder - Block storage — kolla-ansible 19.1.0.dev211 documentation](https://docs.openstack.org/kolla-ansible/latest/reference/storage/cinder-guide.html)
- [Octavia — kolla-ansible 19.1.0.dev211 documentation](https://docs.openstack.org/kolla-ansible/latest/reference/networking/octavia.html)
- [GitHub - osism/openstack-octavia-amphora-image: Machine images for use with the OpenStack Octavia service](https://github.com/osism/openstack-octavia-amphora-image?tab=readme-ov-file)