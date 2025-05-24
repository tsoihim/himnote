## SNMP란
Simple Network Management Protocol (SNMP)는 네트워크 장비를 모니터링하고 설정하기 위한 프로토콜

## 구성요소
SNMP를 지원하는 장비, 즉 관리 대상을 Agent
반대로 SNMP를 이용해 관리 대상을 관리하는 클라이언트는 Manager
디바이스에 SNMP agent가 데몬 등의 형태로 동작하면, 관리시스템은 SNMP Request를 전송하여 모니터링 및 설정 등을 수행할 수 있음

## 동작 상세
### Packet 포맷
- SNMP는 UDP로 동작, 기본적으로 161(ageent), 162(manager) 포트를 사용
- Physical Data Unit (PDU)
    - 실제 SNMP 통해 주고받는 정보를 담고 있는 유닛
    - PDU Types
        - GetRequest : 에이전트에 객체 정보 요청
        - GetNextRequest : 에이전트에 이미 요청한 정보의 다음 정보 요청
        - SetRequest : 에이전트에 특정 값을 설정 요청
        - GetResponse : 에이전트가 객체 정보 응답
        - Trap : 에이전트가 발생시키는 비동기 메시지
- Community
    - 매니저와 에이전트 간 인증을 위한 String 값
![[Pasted image 20250524233307.png|500]]
### 메시지 흐름
![[Pasted image 20250524233324.png|500]]
- 두가지 데이터 수집 방식
    - Polling
        - 동기 Request/Response로 구성
    - Event Reporting
        - 비동기 이벤트 메시지 전달을 위해 Trap 메시지를 사용
### 데이터 구조
**Management Information Base**(MIB)를 사용
- 관리되어야할 자원 객체가 분류된 정보
- 체계화된 관리 정보를 계층 구조로 저장한 것의 집합
**Object ID(OID)**
- 자원 객체의 ID로, 모든 자원 객체는 정수 ID를 가짐
- 벤더별로 실제 데이터는 당연히 다 다를 수 있지만 보통 벤더 상관없이 공통적으로 쓰는 public OID가 있으며, 벤더에 따라 다를 수 있는 private OID가 있음
![[Pasted image 20250524233413.png|500]]
### 버전
4가지 버전 존재
- v1
    - community string match(평문)로 인증
- v2
    - community string match(암호문)로 인증
- v2c
    - community string match로 인증
- v3
    - 단순 community 외에 username, password 추가 (암호화)

## Practice
snmpwalk 툴을 이용하면 CLI를 통해 SNMP Agent에 SNMP Request를 전달할 수 있음
- 사용방법
    - snmpwalk -v {버전명} -c {community 명} {스위치 주소} {OID}
- 아래 예제는 snmpwalk 통해 Centec 스위치에 sysDescr (1.3.6.1.2.1.1.1) 정보를 요청한 것
```bash
root@nara_dev:~ snmpwalk -v 2c -c atto 10.0.93.31 1.3.6.1.2.1.1.1.0
iso.3.6.1.2.1.1.1.0 = STRING: "ATTO_OS software, ATTO-S ATTO-S1GF-8T4X-BP, Version 5.2.5.r1; Copyright (C) 2020 Atto-Research Co., Ltd. All rights reserved. Compiled at May 18 15:08:05 2021."
```


**References**
- [https://securitymax.tistory.com/107](https://securitymax.tistory.com/107)