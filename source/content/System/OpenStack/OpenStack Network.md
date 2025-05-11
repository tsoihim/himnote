## Virtual Networking Options
OpenStack 네트워크는 아래와 같이 두가지로 나뉨

### [_Provider Networks_](https://docs.openstack.org/install-guide/launch-instance-networks-provider.html)
VLAN segmentation 등 L2 기반으로, 가장 단순하게 OpenStack 네트워킹 서비스를 설정하는 방법

OpenStack 가상 네트워크와 실제 물리 네트워크를 연결해주고, 외부의 실제 L3 네트워크 인프라에 의존
- 이에, OpenStack 유저가 외부 네트워크 인프라를 상세히 알아야 가상 네트워크 관리가 가능
부가적으로, DHCP 서비스를 이용하여 각 VM 인스턴스에 IP 주소 제공 등이 가능
![[Pasted image 20250511135258.png|500]]

### [_Self-Service Networks_](https://docs.openstack.org/install-guide/launch-instance-networks-selfservice.html)
VXLAN과 같은 overlay segmentation을 이용하여 provider network에 더하여 L3 서비스까지 포함한 방법

1번 옵션과 달리, [[NAT]]를 이용하여 OpenStack 가상 네트워크를 외부의 실제 네트워크로 라우트함
- 이에, OpenStack 유저가 외부 네트워크 인프라를 상세히 몰라도 가상 네트워크 관리가 가능
- Self-service는 Provider에 후행하여 설정하여야 함
- 단, 외부에서 Self-service 인스턴스에 접근하기 위해서는 floating IP address 설정 필요
- NAT는 아래 모식도의 Router에서 수행

<aside> 💡

Floating IP

- VM이 부트될 때마다 동일한 public IP를 가질 수 있도록 하는 기능 (DNS 유지 등을 위해)
- Pool을 만들어 놓고 VM마다 할당하는 방식으로 동작 </aside>

이 옵션은 LBaaS나 FWaaS 등의 고급 기능의 기반이 됨
![[Pasted image 20250511135422.png|500]]

## [Network types](https://docs.openstack.org/neutron/rocky/admin/intro-os-networking.html)
### Flat
모든 인스턴스가 호스트와 공유될 수 있는 같은 네트워크 안에 존재
VLAN 태깅이나 네트워크 분리가 되지 않음

### VLAN
물리 네트워크와 상응하는 VLAN ID(w/802.1q)를 이용해 여러 네트워크를 생성 가능
인스턴스는 같은 VLAN 내의 네트워크 장비들과 통신 가능

### GRE and VXLAN
GRE 혹은 VXLAN 프로토콜을 통해 Overlay 네트워크를 구성하여 인스턴스간 통신을 활성화하고 제어
오버레이 네트워크 외부와 트래픽을 주고받기 위해서는 라우터가 필요 (인터넷 접근 포함)
또한 라우터 통해 외부에서 floating IP 기반으로 인스턴스에 바로 접근하는 기능도 제공

## Subnets
Subnet은 IP address 블록. native IPAM이며 새 포트가 생성될 시에 IP 할당을 위해 사용
유저는 Subnet Pool을 만들어 자동으로 할당될 IP pool을 미리 만들어 둘 수도 있음

## Security Groups
Ingress/Egress 트래픽 제어를 위한 방화벽 정책 제공, 기본적으로는 특정 트래픽만 오가도록 함
이는 포트 레벨에서 제어되며 각 포트는 복수개의 그룹을 참조할 수 있음