> **netstat** - Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships

### Description
Netstat은 리눅스 네트워크 서브시스템의 정보를 출력해줌. 첫번째 인자로 어떤 종류의 정보를 표출할지 결정할 수 있음
- default : 열려 있는 소켓
- route (-r) : 커널 라우팅 테이블
- groups (-g) : 멀티캐스트 그룹 멤버십
- interfaces (-i) : 모든 네트워크 인터페이스
- statistics (-s) : 프로토콜별 요약

### Options
- p, --program
    - 해당 소켓 사용하는 프로세스까지 표출
- l, --listening
    - 듣기 식별 소켓들만 표출
- a, --all
    - 듣기 식별 소켓 외에 다른 소켓까지 표출

### Usage (example)
```bash
test@nara_dev:~ **netstat lp # 열려있는 모든 듣기 식별 소켓과 매칭 프로세스 표출**
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 10.71.52.7:ssh          10.0.9.2:50080          ESTABLISHED
tcp        0      0 localhost:36704         localhost:41852         ESTABLISHED
tcp        0    200 10.71.52.7:ssh          10.0.9.2:50081          ESTABLISHED
tcp        0      0 localhost:41852         localhost:36704         ESTABLISHED
tcp        0      0 localhost:41854         localhost:36704         ESTABLISHED
tcp        0      0 localhost:36704         localhost:41854         ESTABLISHED
tcp        0      0 10.71.52.7:ssh          10.0.9.2:49837          ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ]         DGRAM                    16256    /run/user/1000/systemd/notify
unix  3      [ ]         DGRAM                    11335    /run/systemd/notify
...
...
```