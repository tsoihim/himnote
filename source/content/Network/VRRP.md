### **VRRP란**
- Virutal Router Redundancy Protocol (VRRP)
- 게이트웨이 이중화 프로토콜
    - Failover를 위해 사용할 수 있음
![[Pasted image 20250524235536.png|500]]

### 기본 동작
- Master, Slave 장비로 구성
- Master, Slave 장비는 하나의 VIP로 묶임
    - VIP를 가진 장비가 Master
- Master 장비의 **선출 기준**
    - VIP와 Real IP가 같은 장비
    - VRRP Priority (우선순위) 값이 큰 장비 (기본값 : 100)
    - Real IP의 주소가 큰 장비
- Master 장비가 죽으면 Slave 장비가 VIP를 가져와 Master 역할 수행

### VRRP Health Check
- VRRP는 하트비트를 전송하여 Master 장비에 대한 헬스 체크 수행
    1. Master 장비는 VRRP Advertisement Packet을 가상 그룹에 반복적으로 전송
        1. VRRP는 Single Virtual Router 방식으로 물리 인터페이스에 설정, 가상 그룹 하나만 생성
            1. VLAN 설정 시 여러개의 그룹 생성 가능
    2. 가상 그룹에 속한 Slave 장비는 해당 패킷을 받는 중에는 Master 장비가 살아있다고 판단
    3. 패킷이 수신되지 않으면 일정 시간(Master Down Timer) 후에 Master가 죽었다고 판단
    4. Slave 에서는 자신이 Master가 되기 위해 VRRP advertisement Packet과 GARP(Gratuitous ARP) Packet 전송
- VRRP는 따로 예약된 멀티캐스트 그룹 주소 사용 (224.0.0.18)


**References**
- [https://limvo.tistory.com/13](https://limvo.tistory.com/13)