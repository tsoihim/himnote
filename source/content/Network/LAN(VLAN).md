## Local Area Network (LAN)
- 스위칭 기술 사용, ARP가 도달하는 범위
- Switch
    - L2 switch: packet switching
    - L3 switch: packet switching (intra), IP packet routing (inter)
- Three tables
    - ARP table, MAC table, Routing table

## Virtual Local Area Network (VLAN)
- 스위치가 지원, 브로드캐스트 도메인을 나눌 수 있음
- Access: 스위치 설정으로 포트별 하나의 VLAN 할당
    - VLAN 개수에 따른 물리적 제약 발생 가능
- Trunk: 프레임 태깅으로 VLAN 구분
    - Native: Trunk port의 untagged VLAN (1 by default)
        ![[Pasted image 20250524232924.png|500]]
- VLAN을 이용한 스위치 설정 예시
  ![[Pasted image 20250524232953.png|400]]

## 통신 시나리오
- 네트워크 환경
    - Spine (L3 switch)
    - Leaf (L2 switch)
    - Two hosts
- 2가지 시나리오
    - Intra/Inter VLAN 통신
- 각각 h1과 h2의 IP 대역이 같거나 다른 경우
![[Pasted image 20250524233051.png|300]]

### Ethernet Switching
- h1 -> h2 패킷 전송
    - h1 : Routing table에서 인터페이스 확인, ARP miss
- ARP Request/Reply
    - L1 : MAC table 기록 및 h1의 request flooding
    - h2 : ARP table 기록 및 reply 전송
    - L1 : MAC table 기록 및 reply 전달
    - h1 : ARP table 기록
- Table lookup을 통한 ethernet packet switching
![[Pasted image 20250524233127.png|300]]

### IP Routing
- h1 -> h2 패킷 전송
    - h1 : Routing table에서 인터페이스 확인, ARP request/reply (on gateway)
    - Spine : mac table에 VLAN, 포트 정보 기록
    - h1 : IP packet gateway로 전송
- IP Routing on gateway
    - Spine : IP 패킷의 MAC dst 확인; Routing table에서 IP dst의 인터페이스 확인
    - Spine : ARP miss; check VLAN table; ARP request/reply (on h2)
    - Spine, h2 : 각각의 ARP 테이블 등에 기록
- Spine의 3 테이블을 통한 IP packet routing
![[Pasted image 20250524233150.png|300]]


**References**
- Netmanias
    - 스위칭과 라우팅... 참 쉽죠잉~ (1편: Ethernet 스위칭)
        - [https://www.netmanias.com/ko/?m=view&id=blog&no=5501](https://www.netmanias.com/ko/?m=view&id=blog&no=5501)
    - 스위칭과 라우팅... 참 쉽죠잉~ (2편: IP 라우팅)
        - [https://www.netmanias.com/ko/?m=view&id=blog&no=5502](https://www.netmanias.com/ko/?m=view&id=blog&no=5502)
    - 1편: 라우터 구조 소개
        - [https://www.netmanias.com/ko/?m=view&id=blog&no=5515](https://www.netmanias.com/ko/?m=view&id=blog&no=5515)
    - 2편: IP 라우터의 패킷 포워딩 과정
        - [https://www.netmanias.com/ko/?m=view&id=blog&no=5521](https://www.netmanias.com/ko/?m=view&id=blog&no=5521)
    - 3편: L3 스위치의 Ethernet 스위칭 과정
        - [https://www.netmanias.com/ko/?m=view&id=blog&no=5526](https://www.netmanias.com/ko/?m=view&id=blog&no=5526)
    - 4편: L3 스위치의 IP 라우팅 과정
        - [https://www.netmanias.com/ko/?m=view&id=blog&no=5529](https://www.netmanias.com/ko/?m=view&id=blog&no=5529)
- [참고자료1](https://m.blog.naver.com/PostView.nhn?blogId=lunarispars&logNo=221440105402&categoryNo=20&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [참고자료2](https://bignet.tistory.com/107?category=650461)
- [참고자료3](https://blog.naver.com/hanoolee/221345231920)
- [참고자료4](https://m.blog.naver.com/PostView.nhn?blogId=sung_mk1919&logNo=221393785227&proxyReferer=https:%2F%2Fwww.google.com%2F)