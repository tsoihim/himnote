Rocky linux 에 kolla ansible 을 이용하여 openstack 설치하는 방법에 대해 정리
LVM을 이용하는 Cinder 를 추가, all-in-one 통해 단일 머신 설치

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
`/etc/kolla/globals.yml` 을 편집한다. 대부분의 경우 기본값을 이용해도 되나, 최소한의 설정을 위해 다음 부분은 수정이 필요하다.
```
workaround_ansible_issue_8743: yes
kolla_internal_vip_address: "10.0.116.240"
network_interface: "ens3"
neutron_external_interface: "ens8"
neutron_plugin_agent: "ovn"
nova_compute_virt_type: "qemu"
enable_cinder: "yes"
enable_cinder_backup: "no"
enable_cinder_backend_lvm: "yes"
```
- `workaround_ansible_issue` 는 uncomment 가 기본값이니 냅두면 되고
- `kolla_internal_vip_address` 는 내부망으로 사용할 vip 로 현재 사용하지 않는 ip 를 넣어주어야 한다. (openstack component 간 이용할 ip 로 보인다.)
- `network_interface` 는 openstack의 여러 component 간 통신할 nic 의 이름이다.
- `neutron_external_interface` 는 br-ex 에 붙을 nic 이름이다. (즉 외부 통신을 위한 interface) -> 여길 통해 외부로 나가는 듯. 다만 이 nic 은 br-ex 에 직접 붙기 때문에 ip 가 소용 없다. (br-ex 에 직접 ip 를 넣는 건 일단 안 보이는 듯)
- `neutron_plugin_agent` ovs를 이용할지, ovn을 이용할지 결정하는 부분. 기본값은 ovs
- `nova_compute_virt_type` kvm이냐 qemu 이냐로, 물리머신일 경우엔 kvm, vm에서 실행할 땐 qemu 로 하면 된다.
- 7번째줄 아래는 상위 문서에서 추가된 내용이다.
- `enable_cinder`로 cinder 도 추가한다.
- `enable_cinder_backup`은 no로 추가한다.
- `enable_cinder_backend_lvm` 은 cinder 의 backend 로 LVM을 이용한다는 것이다.

그 외에 다양한 설정들이 있으니 참고하면 된다.

그런 뒤, 어느 장비에 설치할 것인지 target 을 지정해야 한다. 여기서는 단일 노드로 설치할 것이라, all-in-one 을 이용한다. (이 경우 편집할 필요 없이 그대로 이용하면 된다.)

### **암호 생성**
openstack 에서 사용할 password 들을 지정해 주어야 한다.
```
kolla-genpwd
```
명령을 입력하면, 자동으로 각종 컴포넌트에서 사용할 암호를 자동생성해서 `/etc/kolla/passwords.yml` 에 넣어준다.
이를 바로 이용해도 상관 없지만, 추후 편의를 위해 자주 사용하는 암호는 쉬운 것으로 바꾸는 것을 추천한다.
- database_password : db 접속용 암호
- keystone_admin_password : 설치 후, horizon 에서 접속할 때 사용하는 암호

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
작업이 완료되면, 다음 작업으로 관리를 위한 패키지들을 설치하고 관련 환경설정을 할 수 있다.
```
kolla-ansible post-deploy
pip install python-openstackclient -c <https://releases.openstack.org/constraints/upper/2024.1>
```

여기까지 설치가 완료되었다.

### **기본 설정 (optional)**
설치가 완료된 후, 사용 편의를 위한 기본 설정을 해 줄 수 있다.
기본적인 network 설치, flavor 설치, key 추가, 테스트를 위한 작은 vm인 cirros 이미지 등록 등 openstack 설치 후 초기 설정을 도와주는 작업이다.
```
./kolla/share/kolla-ansible/init-runonce
```
이 작업은 위의 openstack client 가 설치되고, post-deploy 가 설치된 후에 가능하다.
이제 기본 설정까지 마쳤으면 사용에 대한 준비가 완료되었으니 바로 이용할 수 있다.

### 참고 문서

- [Cinder - Block storage — kolla-ansible 19.1.0.dev211 documentation](https://docs.openstack.org/kolla-ansible/latest/reference/storage/cinder-guide.html)