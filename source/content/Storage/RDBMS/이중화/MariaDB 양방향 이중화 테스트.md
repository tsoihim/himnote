### 테스트 설정
- mariadb 양방향 복제 설정
- keepalived 설정
- 클라이언트 테스트

### 양방향 복제 설정
- 두 서버 모두 복제 계정 생성
    - grant replication slave on _._ to ‘rep’@‘10.0.4.%’ identified by ‘atto’;
- 두 서버 모두 /etc/my.cnf 설정
    - log-bin=mysql-bin, server-id=1 (서버 1)
    - log-bin=mysql-bin, server-id=2 (서버 2)
- 두 서버 모두 서비스 재시작
    - systemctl restart mariadb
    - mysql에서 show master status\G로 binlog 파일 명 및 position 확인
        - 양 측 서버 값이 다를 수 있으므로 둘 다 확인
- 두 서버 모두에서 확인한 binlog 파일 명 및 position으로 master 변경작업 수행
    - mysql에서 change master to master_host=‘바라볼 마스터의 IP’,master_user=‘rep’, master_password=‘atto’,master_log_file=‘binlog 파일명’,master_log_pos=‘binlog position’; 입력
    - start slave; 입력 및 서비스 재시작 후 show slave status\G 입력하여 이중화 설정 확인
        - Slave_IO_Running 및 Slave_SQL_Running 필드가 모두 Yes여야 함
![[Pasted image 20250524225834.png|600]]
![[Pasted image 20250524225851.png|600]]
![[Pasted image 20250524225902.png|600]]

### Keepalived 설정
- VIP 사용하여 굳이 양쪽 IP 계속 바라보도록 하지 않기 위함 (특정 테스트에서는 사용하지 않았음)
- 양쪽 서버에 keepalived 설치
    - yum install keepalived
- keepalived 설정
    - /etc/keepalived.conf에서 필요한 설정 세팅
    - 양쪽 서버에서 어떤 것을 감시할지, IP 세팅은 어떻게 할 것인지 등
- keepalived 실행 및 확인
    - systemctl start keepalived
    - systemctl status keepalived
![[Pasted image 20250524225922.png|600]]

### 클라이언트 테스트
- JDBC
![[Pasted image 20250524225944.png|600]]
- MySQL C-API
    - 모든 SQL 쿼리는 디폴트로 서버1에 보낸다
        - Read 쿼리는 서버2로 보내도 됨
    - 서버1에서 응답 없을 경우 에러 코드 확인 후 서버2로 쿼리 포워딩
        - Close 제외한 모든 mysql 쿼리에 대하여 오류 생긴 경우 연결 전환해줘야함
        - 이를 핸들링하는 함수 추가 (failover)
        - Mysql error 발생 시 errno와 기존 conn을 위 함수에 넘겨주고, 연결 전환하도록 함
        - 만약에 서버2까지 뻗을 경우 error 로그 출력시키도록 함
    - failover(MYSQL *conn, uint32_t errno)
        - Mysql error 발생 시 errno와 기존 conn을 매개변수로 받아 호출됨
        - connection 에러임을 확인
            - 아닐 경우 return
            - [mysql client 에러 코드](https://dev.mysql.com/doc/mysql-errors/8.0/en/client-error-reference.html#error_cr_server_gone_error) (2002, 2003, 2006, 2013)
        - mysql_get_host_info()를 통해 현재 커넥션 확인
            - 서버 1이라면 연결 종료 후 서버 2로 재연결
        - 서버 2에서 에러난 쿼리 재실행
            - 만약에 서버2까지 뻗었을 경우 error 로그 출력시키고 exit
    ![[Pasted image 20250524230025.png|300]]

### 정합성 테스트
![[Pasted image 20250524230045.png|600]]