### LLDP란
- Link Layer Discovery Protocol (LLDP)
- 네트워크 장비 간 링크 연결을 확인하기 위하여 사용

### 동작방식
- 네트워크 장비에서 LLDP 관련 설정
```bash
JIPSUN2(config)# lldp enable
```
- 네트워크 장비는 인터페이스로 LLDP를 주기적으로 전송
- 네트워크 장비가 LLDP 패킷을 전송받으면 이를 파싱하여 Neighbor 정보를 기록
```bash
JIPSUN2# show lldp neighbor brief
Local Port  : eth-0-11
Remote Port : eth-0-11
Hold Time   : 120
Expire Time : 110
System Name : JIPSUN1
```

### 패킷 포맷
- Ethernet Frame

| dl_dst       | 01:80:c2:00:00:0e | LLDP multicast      |
| ------------ | ----------------- | ------------------- |
| dl_src       | xx:xx:xx:xx:xx:xx | switch port hw addr |
| ether-type   | 0x88cc            | ether type          |
| LLDP Payload |                   |                     |

- LLDP TLV 예시
    - 여러 TLV의 집합
    - 벤더 specific TLV도 추가 가능

|TLV NAME|Subtype|Value|
|---|---|---|
|CHASSIS_ID|CHASSIS|chassis ID 값|
|PORT_ID|Interface Name|interface name|
|TTL|-|2 * ld_interval|
|PORT_DESC|-|port_no|
|SYSTEM_NAME|-|dpid|
|SYSTEM_DESC|-|software + manufacturer|
|END|||