### Network Address Translation란?
- Network Address Translation(NAT)
- Inter-vlan 통신에서 게이트웨이가 mac-rewriting을 하듯이, IP 패킷에 대하여 IP 주소 및 Port를 rewriting하며 통신할 수 있도록 하는 방법

### 왜 쓰는가?
- IP 절약 가능
- 내부 IP가 숨겨지므로 보안 향상

### 주소 할당 방식
- Traditional NAT
    - 변환
        - 송신 패킷 : src IP A, dst IP B
        - src IP와 dst IP를 매핑 테이블에 기록
        - 공인 IP C를 이용하여 패킷 변환하여 내보냄
            - 변환 패킷 : src IP C, dst IP B
    - 포워딩
        - 수신 패킷 : src IP B, dst IP C
        - 매핑 테이블 이용하여 패킷 변환하여 내부 포워딩
            - 변환 패킷 : src IP B, dst IP A
- Port Address Translation (PAT) or Network Address Port Translation (NAPT)
    - 내부 호스트가 같은 목적지 주소 향하는 경우 매핑 테이블의 unique성이 깨짐
    - 따라서 port 번호까지 이용하여 매핑 수행
- etc.
    - Static NAT, Dynamic DAT