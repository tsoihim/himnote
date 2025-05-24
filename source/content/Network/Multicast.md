# IP Multicast
- 한 그룹에 소속된 특정 다수에게 데이터를 전송하는 기법
    - 그룹에 소속되면 멀티캐스트 데이터를 전달받을 수 있음
    - 특정 수신자 IP 대신 그룹 IP를 이용하여 데이터 전송
        - 그룹 IP로 전송하면 그룹 내 모든 멤버가 수신
  ![[Pasted image 20250524233910.png|400]]
- Multicast Addresses (*그룹이 공유하는 주소)
    - Multicast IP : D-class IP 주소 사용(224.0.0.0~239.255.255.255)
    - Multicast MAC : “01:00:5E”로 시작
        - 위 IP 주소의 끝에서 23개 비트를 “00000001. 00000000. 01011110. 0” 뒤에 붙임
        - 224.0.0.1: 전체 호스트, 224.0.0.2: 전체 라우터
    ![[Pasted image 20250524233957.png|400]]
- Multicast Group Membership Discovery Protocol
    - 호스트가 멀티캐스트 라우터를 통해 그룹에 등록되거나 해지될 수 있도록 관리
    - IGMP, MLD
- Multicast Routing Protocol
    - 라우터끼리 통신하여 멀티캐스트 데이터가 소스로부터 호스트까지 전달될 수 있도록 경로 설정
    - MOSPF, PIM

# IGMP
- Internet Group Management Protocol (IGMP)
    - 호스트와 L3 장비(multicast router) 간 multicast 정보를 주고받는데 사용
        - 특정 LAN에서 multicast 그룹에 등록되는 호스트 관리
    - IP 패킷에 캡슐화되어 전송
    ![[Pasted image 20250524234040.png|500]]
- IGMP Messages
    - Membership Report : 그룹 가입·멤버십 유지 보고
    - General Query : 멤버십 유지 여부 조사
    - Leave(from v2) : 그룹 탈퇴 시 전송
    ![[Pasted image 20250524234109.png|500]]
- IGMP 작동 프로세스 (*subnet 단위로 작동)
    - 라우터는 주기적인 query 전송(to 224.0.0.1)을 통해 그룹 멤버쉽 조사
        - 호스트가 먼저 그룹 가입 메시지를 전송(to group_addr)할 수도 있음
    - 특정 멀티캐스트 그룹에 유지되기를 원하는 경우,query에 대하여 호스트가 report 전송
    - 일정 기간 report를 수신하지 못했거나, leave 메시지(to 224.0.0.2)를 전송한 서브넷에는 포워딩 중지
    ![[Pasted image 20250524234141.png]]
### IGMP Snooping
- L2 장비에서의 multicast flow flooding 방지
    - 스위치가 multicast에 속한 호스트에만 트래픽 보내도록 해줌
- L2 장비가 IGMP 패킷(report)을 snoop
    - IGMP Snoop enable 설정
    - IGMP Report 메시지를 수신한 포트와 주소 기록
        - 특정 Multicast 그룹에 대하여 패킷을 전달할 포트를 학습
        - Leave 메시지를 수신한 포트는 반대로 테이블에서 삭제
    - IGMP Query 메시지를 수신한 포트는 multicast 라우터 포트로 기록
![[Pasted image 20250524234333.png|300]]
![[Pasted image 20250524234400.png|300]]
- Report/Leave Suppression
    - L2 장비가 서브넷에서 수신 중인 멀티캐스트 스트림에 대한 report 혹은 leave 메시지의 중복되는 전송을 막음
    - Report, Leave가 suppress 되는 경우
![[Pasted image 20250524234309.png|600]]

# PIM
- Protocol Independent Multicast (PIM)
    - Unicast routing table 정보에 의존
    - Source Based Tree (PIM-DM), Center Based Tree(PIM-SM)
### PIM-DM
- Protocol Independent Multicast-Dense Mode
    - 소스 인접 라우터가 루트가 되어 트래픽 전달
    - 라우터들은 멀티캐스트가 활성화된 모든 인터페이스로 트래픽 flooding
    - 트래픽을 수신할 필요가 없는 라우터는 prune을 업스트림 라우터에 회신
        - 트래픽을 수신할 다운스트림 라우터 혹은 직접 연결된 호스트가 없는 경우
        - non-RPF(Reversed Path Forwarding) 인터페이스로부터 수신된 경우
    - 위 과정을 주기적으로 반복
![[Pasted image 20250524234451.png|400]]
### PIM-SM
- Protocol Independent Multicast-Sparse Mode
    - 다운스트림으로부터의 요청이 있기 전까지는 멀티캐스트 트래픽을 포워딩하지 않는 방식
    - 방송 중인 모든 멀티캐스트 소스 정보를 가진 Rendezvous Point (RP) 지정
        - 소스 인접 라우터는 멀티캐스트 트래픽을 RP로 전달
        - 트래픽을 수신하려는 호스트를 가진 라우터는 RP에게 이를 요청
        - RP 지정 방식: static, auto, BSR
- Source to RP (RP에 소스 정보 등록)
    - PIM Register, PIM Register NULL (재요청의 경우)
    - PIM Register Stop (Receiver가 없는 경우)
![[Pasted image 20250524234509.png|400]]
- Receiver to RP
    - Receiver가 있거나 downstream 라우터로부터 PIM Join 받으면 RP로 전송
- 경로 설정
    - R6가 소스의 주소를 알게되었으므로 소스로 PIM Join 전송하여 경로 새로 구성 가능
        - 기존의 경로(to RP)에는 PIM Prune을 전송하여 사이에 있는 라우터들이 전달을 중지
![[Pasted image 20250524234603.png|500]]


**References**
- [https://www.netmanias.com/ko/post/blog/5673/igmp-iptv/igmp-snooping-for-iptv-service](https://www.netmanias.com/ko/post/blog/5673/igmp-iptv/igmp-snooping-for-iptv-service)
- [https://networklessons.com/multicast/multicast-pim-sparse-mode](https://networklessons.com/multicast/multicast-pim-sparse-mode)
- [https://osrg.github.io/ryu-book/ko/html/igmp_snooping.html](https://osrg.github.io/ryu-book/ko/html/igmp_snooping.html)
- [https://netcraftsmen.com/pim-sparse-mode/](https://netcraftsmen.com/pim-sparse-mode/)

from 21.04.16