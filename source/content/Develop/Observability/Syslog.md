## Syslog란
이벤트 알람 메시지를 전달하기 위해 사용되는 프로토콜
서버, 스위치 등 다양한 장비에서 발생하는 이벤트를 전달하기 위해 쓰임
전달된 내용은 syslog를 수집하는 로깅 서버로 전달될 수 있음

### 패킷 구조
아래는 Log4j에서 Syslog Appender를 이용하여 syslog 서버에 전달한 패킷의 일부
버전~MessageId 등의 필드들로 구성되어 있으며, syslog는 vendor-specific한 포맷도 허용함
![[Pasted image 20250517194102.png|600]]

## 어플리케이션 예제
### 구성요소
- Rsyslogd
- Java application (w/Log4j)
- MariaDB
### Rsyslogd
로깅 서버로는 Rsyslogd를 활용
- mysql plugin을 장착하여 전달받은 syslog를 RDB에 작성하도록 함
- 아래의 설정 사용
```
global(workDirectory="/var/lib/rsyslog")
module(load="builtin:omfile" Template="RSYSLOG_TraditionalFileFormat")
include(file="/etc/rsyslog.d/*.conf" mode="optional")
module(load="imudp")
input(type="imudp" port="514")
module(load="omprog")
module(
  load="impstats"
  interval="10"
  format="json"
  resetCounters="off"
  ruleset="process_stats"
)
ruleset(name="process_stats") {
  action(
    type="omprog"
    name="to_exporter"
    binary="/usr/bin/rsyslog_exporter --web.listen-address :9104"
  )
}
module (load="ommysql")
*.* action(type="ommysql" server="$MARIADB_IP" db="$MARIADB_DATABASE" uid="$MARIADB_USER" pwd="$MARIADB_PASSWORD")
```
### MariaDB
Rsyslog의 mysql 플러그인을 설치하면 아래의 파일을 확인 가능
- /usr/share/doc/rsyslog/mysql-createDB.sql
해당 sql에서 필요한 column만을 추출하여 MariaDB에 테이블 생성해줌
```sql
CREATE TABLE SystemEvents (
        ID BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
        ReceivedAt DATETIME NULL,
        DeviceReportedTime DATETIME NULL,
        Facility SMALLINT NULL,
        Priority SMALLINT NULL,
        FromHost VARCHAR(60) NULL,
        Message VARCHAR(4096),
        InfoUnitID INT NULL ,
        SysLogTag VARCHAR(60),
        PRIMARY KEY (ID, DeviceReportedTime)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4

CREATE TABLE SystemEventsProperties (
        ID int unsigned not null auto_increment primary key,
        SystemEventID int NULL ,
        ParamName varchar(255) NULL ,
        ParamValue text NULL
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
### Java application
Log4j2는 Marker와 Syslog Appender를 지원, 이를 이용해 특정 로그만 필터링하여 Syslog 서버로 전송하는 것이 가능
아래와 같은 Log4j2 설정 파일 사용
- log 메소드 호출 시 인자로 로그 내용뿐만 아니라 Marker도 같이 넘겨줌
- 특정 Marker(SYSLOG)가 포함된 로그만 syslog 서버로 전송하도록 설정
```yaml
Configuration:
  MarkerFilter:
    marker: "SYSLOG" # Marker로 특정 로그만 필터링
    onMatch: "ACCEPT"
    onMismatch: "NEUTRAL"
  Appenders:
    Syslog:
      name: Syslog_Appender
      host: 172.26.37.110
      port: 514
      protocol: UDP
      facility: USER # user-level messages
      appName: ${APP_NAME}
      charset: UTF-8
      newLine: true
      format: RFC5424
  Loggers:
    Logger:
    - name: [패키지 경로]
      AppenderRef:
        ref: Syslog_Appender
        MarkerFilter:
          marker: "SYSLOG"
```
Log4j2의 composite configuration을 활용하면 아래 명령과 같이 기존 설정에 상기 설정을 혼합한 형태로 Java 어플리케이션을 실행시킬 수 있음
- `java -Dlog4j.configurationFile=classpath:log4j2.yml,classpath:log4j2-syslog.yml -jar [JAR 파일명]`


### References
- https://datatracker.ietf.org/doc/html/rfc5424
- https://www.rsyslog.com/
- https://logging.apache.org/log4j/2.x/manual/appenders/network.html
- https://logging.apache.org/log4j/2.x/manual/configuration.html