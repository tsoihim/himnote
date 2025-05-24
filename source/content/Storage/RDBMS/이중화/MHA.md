![[Pasted image 20250524230129.png|500]]
master/slave로 구성(mha manager는 슬레이브에 설치), vip 사용
- 이 경우 slave 서버에 문제 생기면 그냥 standalone이나 다름없긴 함

### **MHA**
master가 서비스 불가능할 때, slave를 master로 숭격시켜 서비스를 유지시켜주는 perl 기반 auto-failover 솔루션. Mha node, Mha manager로 구성됨.

직접 스크립트 생성 및 수정하여 로직 개선할 수 있음
- Node : 로컬 에이전트, mysql 인스턴스 모니터링 수행, 매니저가 슬레이브로부터 relay 로그 카피하도록 함
- Manager : failover 관리, 언제 어디에 failover 수행할지 결정
- 모든 노드에 mesh로 Ssh-copy-id 미리 해놓아야함

### **장애 체크**
MHA Manager가 3초마다 마스터 DB를 CONNECT / SELECET / INSERT 체크하며 3회 실패시 장애로 인식하고 Failover를 수행

### Failover
- 장애 발생시 가장 최근에 변경된 Slave를 Master DB로 승격 시킨 후 Lossless Replication 를 통한 relay log로 데이터 복구를 수행
- Master에 장애 발생 시 manager가 master 장비의 vip down시킴 이후 replication 진행한 다음 down 시킨 vip를 slave 장비에서 올려 failover진행

### **설치 및 설정**
- [가이드](https://lab.cliel.com/entry/2020-02-07-CentOS-MariaDB-MHA-%EC%84%A4%EC%A0%95)


**기타 참고**
- [mysql 이중화](https://velog.io/@jwpark06/MySQL-%EC%9D%B4%EC%A4%91%ED%99%94-%EC%A7%84%ED%99%94%EA%B8%B0)
- [mha 소스코드](https://github.com/yoshinorim/mha4mysql-manager)