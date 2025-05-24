## VXLAN 개요
- Virtual eXtensible Local Area Network (VXLAN)
    - VLAN을 확장하는 개념
    - Network Virtualization over Layer 3 (NVO3)
    - 네트워크 장치들 사이에 IP로 통신하는 논리적인 터널을 개설
    - Layer 2 이더넷 프레임에 여러 헤더를 캡슐화하여 Layer 3로 전송
![[Pasted image 20250524232013.png|500]]

## VXLAN 등장배경
- 가상화 기반 서버 환경에서 나타나는 VLAN의 한계
    - 12bit VLAN ID 기반 4095개 VLAN의 개수적 한계
    - Trunk 기반 VLAN에서의 VM 이동성 부족
    - 너무 많은 VLAN으로 인한 MAC address table 관리 문제
- VXLAN의 해결 방안
    - 24bit VXLAN Network Identifier (VNI)로 16,777,215개 세그먼트 제공
        
    - VM이 연결된 스위치의 포트가 바뀌어도 IP는 바뀌지 않을 수 있음에서 착안,
        
        논리적인 overlay 터널을 생성하여 underlay 연결 상태에 구애받지 않도록 구성
        
    - 가상 스위치를 생성하여 VXLAN용 MAC 주소를 분산        

## VTEP
- Virtual Tunnel End Point (VTEP)
    - VXLAN 통신의 소스와 데스티네이션
    - 이더넷 패킷을 캡슐화, 역캡슐화
    - 물리적인 스위치일 수도, 가상 스위치일 수도 있음
    - 네트워크 내의 모든 VTEP은 서로 연결되어있음
![[Pasted image 20250524232044.png|600]]

## VXLAN 패킷
- VXLAN 캡슐화 / 역캡슐화
    - VXLAN header
        - VNI를 내포하여 VXLAN 구분
    - UDP header
        - VXLAN 소스와 데스티네이션 VTEP의 포트 번호 지정
    - Outer IP header
        - VXLAN 소스와 데스티네이션 VTEP의 IP 주소 지정
    - Outer MAC header
    - VXLAN 소스의 MAC 주소와 Underlay 경로의 넥스트 홉의 MAC 주소 지정
![[Pasted image 20250524232113.png|500]]

## VXLAN 게이트웨이
- VXLAN 게이트웨이 종류
    - Layer 2 게이트웨이 : VXLAN 네트워크 내에서 인트라 통신 담당 (vlan?)
    - Layer 3 게이트웨이 : VXLAN 네트워크와 외부 네트워크 간 인터 통신
- VXLAN 게이트웨이 배치 방법
    - Centralized
        - Spine에 Layer 3 게이트웨이 하나만 설치
        ![[Pasted image 20250524232153.png|400]]
    - Distributed
        - Leaf들에 Layer 3 게이트웨이 각각 설치
        ![[Pasted image 20250524232240.png|400]]    
- 게이트웨이 배치 방법 간 장단점
    - Centralized 방법은 구성과 관리가 쉬우나, vlan 통신이 옵티멀하지 않음 (파란색 트래픽)

## VXLAN 패킷 포워딩
- Topology
  ![[Pasted image 20250524232322.png|400]]
- 인트라 서브넷 커뮤니케이션
    - VM_A가 VM_C와의 통신을 요청하는 경우
  ![[Pasted image 20250524232339.png|400]]
  ![[Pasted image 20250524232530.png|400]]
- 인터 서브넷 커뮤니케이션
    - VM_A가 VM_B와의 통신을 요청하는 경우
  ![[Pasted image 20250524232615.png|400]]  
  ![[Pasted image 20250524232652.png|500]]
- VTEP과 VNI 기반 멀티캐스트
    - Broadcast, Unknown unicast, Multicast (BUM) 트래픽
    - VTEP은 VNI와 멀티캐스트 그룹을 매칭해놓고, BUM 트래픽을 받으면 해당하는 VNI를 갖고 있는 VTEP에만 멀티캐스트함
![[Pasted image 20250524232747.png|500]]
![[Pasted image 20250524232803.png|300]]


**References**
- [유튜브 정리자료](http://youtube.com/watch?v=YNqKDI_bnPM)
- [화웨이 정리자료](https://support.huawei.com/enterprise/en/doc/EDOC1100086966)
- [블로그 정리자료](https://youngmind.tistory.com/entry/Network-Overlay-VXLAN-%EB%B6%84%EC%84%9D-1?category=394664)