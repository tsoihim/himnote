### Multi-chassis Link Aggregation Group (MLAG)이란?
LAG을 여러 샤시에도 적용할 수 있도록 하여, 여러 물리적인 샤시를 논리적인 한 샤시로 보이게 하는 기술
![[Pasted image 20250524235327.png|500]]

### 설정 방식
- Static
    - 동일 벤더간 연결 시 사용
- Dynamic
    - 이기종간 연결 시 사용
    - active/passive 모드로 나뉨

### 왜 하나로 묶는가?
- 대역폭 상승
- 링크 단절 위험 회피
- 로드 밸런싱
- 비용 절감 (대역폭 늘리기 위해 상위 라인업으로 교체가 필요 없음)

### 주의사항
- 짝수 개의 포트만 묶을 수 있음


**References**
- [https://docs.pica8.com/display/PicOS440sp/Principle+of+MLAG](https://docs.pica8.com/display/PicOS440sp/Principle+of+MLAG)