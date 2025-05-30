### LACP란
여러 물리 포트를 묶어 하나의 논리적 포트로 동작하게 하는 Link Aggregation Group (LAG)을 셋업할 수 있도록 하는 제어 프로토콜
- IEEE 802.3ad

### 설정 방식
- Static
    - 동일 벤더간 연결 시 사용
    - LACP를 쓰지 않는 것 (단순 고정 LAG)
- Dynamic
    - 이기종간 연결 시 사용
    - active/passive 모드로 나뉨
        - Active : 상대 인터페이스로 LACP 패킷을 정기적으로 전송
        - Passive : 상대방이 전송하는 LACP 패킷을 대기

### 왜 하나로 묶는가?
- 대역폭 상승
- 링크 단절 위험 회피
- 로드 밸런싱
- 비용 절감 (대역폭 늘리기 위해 상위 라인업으로 교체가 필요 없음)

### 주의사항
- 짝수 개의 포트만 묶을 수 있음
- lacp로 묶여있는 링크가 있다면 플래핑의 여지는 없는지 늘 주의하여 확인해주자