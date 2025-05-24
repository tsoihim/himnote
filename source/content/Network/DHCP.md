# DHCP란
- Dynamic Host Configuration Protocol (DHCP)
    - IP를 수동 설정하지 않고 동적으로 IP를 할당하는 기법
    - IP를 영구적으로 할당하는 것이 아닌, 일정 기간(IP lease time) 동안 임대하는 개념
        - 이 임대기간은 연장/종료가 가능하며 할당받은 IP를 반납할 수 있음
- DHCP Server는 일정한 IP 대역을 가지고, 네트워크 내의 장치에 IP를 임대
    - 클라이언트와 DHCP 메시지를 교환하여 IP 임대 수행
        - Discover, Offer 등의 DHCP 메시지를 교환
![[Pasted image 20250524233637.png|500]]

# DHCP 메시지
- DHCP 패킷은 UDP로 전송됨
    - 포트 67번(server), 포트 68번(client) 사용
    - 전송할 내용의 상당 부분이 옵션에 포함됨
    ![[Pasted image 20250524233735.png|400]]
- DHCP Discover
    - 장비가 DHCP 서버를 찾기 위한 메시지 (클라이언트 -> 서버)
    - DHCP 서버의 응답을 요청 (브로드캐스트)
- DHCP Offer
    - DHCP 서버가 존재함을 알리고, 할당할 IP 주소 정보를 포함한 네트워크 정보 전송 (서버 -> 클라이언트)
    - 수신한 Discover 메시지의 Broadcast Flag 값에 따라 브로드캐스트이거나 유니캐스트
    - DHCP 서버는 ICMP echo 요청을 통해 Your IP를 이미 가지고 있는 호스트가 있는 지 확인
        - 다른 서버가 이미 Your IP를 할당했을 수도 있기 때문
    - 주요 파라미터
        - 단말에 할당할 IP 주소 (Your IP), Subnet Mask, 단말의 Default Gateway IP, DNS 서버 IP, IP Lease Time, DHCP Server Identifier
- DHCP Request
    - 단말이 DHCP 서버의 존재를 알았고, 하나의 DHCP 서버에 단말이 사용할 네트워크 정보를 요청 (클라이언트 -> 서버, broadcast)
    - IP lease time을 갱신할 때도 사용
    - 주요 파라미터
        - 클라이언트가 할당받으려는 IP 주소 (Requested IP, DHCP Offer의 Your IP), DHCP Server Identifier
- DHCP Decline
    - 클라이언트가 자신이 offer 받은 주소를 ARP request를 통해 중복 여부 검사
    - 중복 주소일 경우 거부 및 재요청 (클라이언트 -> 서버)
- DHCP Ack
    - DHCP 서버가 단말에게 최종적으로 네트워크 정보를 전달 (서버 -> 클라이언트)
        - 그 외에는 DHCP NAK 전송
    - 수신한 Discover 메시지의 Broadcast Flag 값에 따라 브로드캐스트이거나 유니캐스트
    - 주요 파라미터
        - Client MAC, Your IP, Subnet Mask, 단말의 Default Gateway IP, DNS 서버 IP, IP Lease Time, DHCP Server Identifier
- DHCP Release
    - 클라이언트가 서버에게 자신이 할당받은 IP 반환 요청 (클라이언트 -> 서버)

# Relay Agent
- 모든 서브넷에 DHCP 서버를 두기 힘드므로 다른 서브넷에 위치한 DHCP 서버와 단말 간 DHCP 메시지를 교환할 수 있도록 라우터에 설정
    - 즉, 단말이 송신하는 DHCP 브로드캐스트 패킷을 유니캐스트로 변환해 DHCP 서버에 전달
    - DHCP Discover
        - 단말이 보낸 브로드캐스트 패킷을 Relay Agent가 유니캐스트로 변환(SIP=Relay Agent, DIP=DHCP Server) 후 DHCP 서버로 전달
    - DHCP Offer
        - DHCP 서버가 DHCP Relay Agent로 유니캐스트(SIP=DHCP 서버, DIP=Relay Agent)로 보내면 이를 수신한 Relay Agent는 브로드캐스트로 변환 후 단말에 전송(반드시 브로드캐스팅은 아님)
    - DHCP Request
        - 단말이 보낸 브로드캐스트 패킷을 Relay Agent가 유니캐스트로 변환(SIP=Relay Agent, DIP=DHCP Server) 후 DHCP 서버로 전달
    - DHCP Ack
        - DHCP 서버가 DHCP Relay Agent로 유니캐스트(SIP=DHCP 서버, DIP=Relay Agent)로 보내면 이를 수신한 Relay Agent는 브로드캐스트로 변환 후 단말에 전송(반드시 브로드캐스팅은 아님)
![[Pasted image 20250524233810.png|500]]


**References**
- [https://datatracker.ietf.org/doc/html/rfc2131](https://datatracker.ietf.org/doc/html/rfc2131)
- [https://datatracker.ietf.org/doc/html/rfc2132](https://datatracker.ietf.org/doc/html/rfc2132)
- [https://www.iana.org/assignments/bootp-dhcp-parameters/bootp-dhcp-parameters.xhtml](https://www.iana.org/assignments/bootp-dhcp-parameters/bootp-dhcp-parameters.xhtml)
- [https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp](https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp)
- [https://www.netmanias.com/ko/post/blog/5366/dhcp-ip-allocation-network-protocol/what-is-a-dhcp-relay-agent](https://www.netmanias.com/ko/post/blog/5366/dhcp-ip-allocation-network-protocol/what-is-a-dhcp-relay-agent)
