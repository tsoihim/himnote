MariaDB에서 두 가지(master/master, master/slave)의 이중화 구성에 대하여 정리

## **1. 양방향 이중화**
### **설계 및 구성**
두 개의 DB 서버; 서버1과 서버2를 active-active로 구성
이중 마스터 혹은 양방향 복제라고 함 (i.e., 공동 마스터 한 쌍, 서로가 마스터임과 동시에 슬레이브)
주로 하나의 마스터 서버만 사용하고, 문제가 발생한 경우에만 다른 마스터 서버(active, standby)로 연결 전환하므로 서버 shutdown 등에 대한 대처가 빠름.
![[Pasted image 20250524224818.png|400]]
### **동작 방식**
클라이언트가 쿼리를 날릴 때 서버에 아무 이상이 없을 경우 write는 서버1에서만 처리됨. 해당 서버가 응답이 없을 경우, 서버2에서 쿼리가 처리됨.
### **설치 및 설정**
데이터 충돌 등을 피하기 위하여 권장되는 안정적인 복제 구성 및 설정을 하여야함.
한 서버에 read_only 설정을 하여 아예 passive master로 설정해놓을 수도 있음.
- [binlog 시스템 변수 리스트](https://mariadb.com/kb/en/replication-and-binary-log-system-variables/)
    - 주요 변수: server_id, log-bin, sync-binlog, binlog_format
        - binlog 관련
            - log-bin
                - Enable binlog (= ON)
            - server_id
                - 복제 구성에서 unique한 primary와 replica 들을 구별하기 위해 설정
            - binlog_format
                - ROW, STATEMENT, or MIXED
            - sync-binlog
                - N개의 커밋마다 binlog를 디스크에 동기화
            - sync-relaylog
                - N개의 커밋마다 binlog를 디스크에 동기화
            - relay_log_recovery
                - Replica가 재시작될 때 프로세스 되지 않은 relay log는 버리고 master에게 재요청
        - semi-sync 관련
            - rpl_semi_sync_master_enabled
            - rpl_semi_sync_master_timeout
                - 지정한 timeout 내에 slave로부터 응답이 오지않으면 async로 전환
            - rpl_semi_sync_master_wait_point
                - after_sync 혹은 after_commit로 설정
            - rpl_semi_sync_slave_enabled
    - 복제방식
        - Binlog_pos 방식과 gtid 복제 방식 중 택일
        - Row based, Statement based, or mixed
- DB간 동기화방식
    - Async
    - [semi-sync](https://mariadb.com/kb/en/semisynchronous-replication/#enabling-semisynchronous-replication) : relay log에 기록이 완료되었다는 신호를 받은 후 트랜잭션 결과를 반환
        - after_commit과 after_sync 방식이 있음
    ![[Pasted image 20250524224906.png|500]]
### **제약사항 및 이슈**
클라이언트를 신뢰하는 방식의 구성이므로, 양쪽 서버에서 동시에 쓰기 작업이 수행되는 일이 절대 없어야 한다.
복구 됐을 때 동기화에서 문제 발생 가능성, 다른 서버로 연결 전환 시 commit 문제
(하위문서 양방향 이중화 테스트 참고)

## **2. 단방향 이중화**
### **설계 및 구성**
두 개의 DB 서버; 서버1과 서버2를 master-slave 혹은 active/standby로 구성 (단방향 복제)
### **동작 방식**
특정 VIP를 사용하여 서버1에서 장애 발생 시 VIP가 서버2로 전환되도록 함
- Auto-failover
    - active master가 뻗을 경우 자동으로 passive를 active로 promote하거나 slave를 master로 승격
### **설치 및 설정**
- MHA
- [simple master/slave](https://ongamedev.tistory.com/entry/mariadbmysql-%EC%9D%B4%EC%A4%91%ED%99%94-%EA%B5%AC%EC%84%B1) (테스트 및 확인)
- [MySQL HA&DR (toss)](https://toss.im/slash-21/sessions/2-3)


**References**
- [https://9d4u.tistory.com/670](https://9d4u.tistory.com/670)