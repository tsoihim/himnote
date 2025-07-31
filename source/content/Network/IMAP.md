## **개념**
### **IMAP이란**
인터넷 메시지 접근 프로토콜의 약어로, 클라이언트가 서버에 보관된 메일에 접근하고 관리할 수 있도록 지원. 로컬 폴더와 같이 메일박스를 관리할 수 있고, 오프라인 클라이언트가 서버와 재동기화할 수 있도록 함

서버 중심의 프로토콜로 다중 클라이언트와 동기화에 중점을 두고, POP3와 달리 클라이언트가 메일을 다운로드하여도 서버에서 메일이 삭제되지 않음

현재까지 버전 1, 2(각각 RFC3501, RFC9051)가 있음

### **IMAP 동작 방식**
IMAP은 TCP 기반으로 텍스트를 송수신하여 동작
- 기본 포트는 143, SSL 포트는 993

클라이언트와 서버는 명령와 응답을 주고 받으며, 텍스트는 ASCII를 사용한 Line-based 텍스트를 사용
- 개행은 [[CRLF]] 사용하여 구분됨
```
   Example:    C: a001 CAPABILITY
               S: * CAPABILITY IMAP4rev1 STARTTLS LOGINDISABLED
               S: a001 OK CAPABILITY completed
               C: a002 STARTTLS
               S: a002 OK Begin TLS negotiation now
               <TLS negotiation, further commands are under [TLS] layer>
               C: a003 CAPABILITY
               S: * CAPABILITY IMAP4rev1 AUTH=PLAIN
               S: a003 OK CAPABILITY completed
               C: a004 LOGIN joe password
               S: a004 OK LOGIN completed
```

IMAP 연결 및 명령 수행 다이어그램은 아래와 같음
```
                   +----------------------+
                   |connection established|
                   +----------------------+
                              ||
                              \/
            +--------------------------------------+
            |          server greeting             |
            +--------------------------------------+
                      || (1)       || (2)        || (3)
                      \/           ||            ||
            +-----------------+    ||            ||
            |Not Authenticated|    ||            ||
            +-----------------+    ||            ||
             || (7)   || (4)       ||            ||
             ||       \/           \/            ||
             ||     +----------------+           ||
             ||     | Authenticated  |<=++       ||
             ||     +----------------+  ||       ||
             ||       || (7)   || (5)   || (6)   ||
             ||       ||       \/       ||       ||
             ||       ||    +--------+  ||       ||
             ||       ||    |Selected|==++       ||
             ||       ||    +--------+           ||
             ||       ||       || (7)            ||
             \/       \/       \/                \/
            +--------------------------------------+
            |               Logout                 |
            +--------------------------------------+
                              ||
                              \/
                +-------------------------------+
                |both sides close the connection|
                +-------------------------------+
         (1) connection without pre-authentication (OK greeting)
         (2) pre-authenticated connection (PREAUTH greeting)
         (3) rejected connection (BYE greeting)
         (4) successful LOGIN or AUTHENTICATE command
         (5) successful SELECT or EXAMINE command
         (6) CLOSE command, or failed SELECT or EXAMINE command
         (7) LOGOUT command, server shutdown, or connection closed
```

### **IMAP 명령**
IMAP 명령(커맨드)은 미리 정의된 텍스트 기반 지시사항으로, 클라이언트나 서버에서 수행해야 할 작업과 이에 수반된 데이터 처리에 대한 명세로 아래와 같은 구조를 가짐

포맷 : `[태그] [명령] [인자]\r\n`
- 위 명령에 따라 응답형식은 상이할 수 있음

커맨드 종류 (일부)
- `LOGIN`
- `LOGOUT`
- `AUTHENTICATE`
- `SELECT`
    - 메일박스를 선택하여 내부 메시지에 접근 가능하도록 함
- `EXAMINE`
    - SELECT와 동일 결과를 반환하나 메일박스에 Read-Only로 접근
- `FETCH`
	- 메시지 데이터 조회
- `STORE`
    - 메일에 조회 여부 등의 플래그 설정

\*위 다이어그램에 정의된 상태에 따라 수행가능한 커맨드도 달라짐

상기 명령을 수행 시 서버는 OK, NO(주의 혹은 커맨드 실패), BAD(프로토콜 레벨 에러) 등의 응답을 전달


***References***
- [RFC 3501 - INTERNET MESSAGE ACCESS PROTOCOL - VERSION 4rev1](https://datatracker.ietf.org/doc/rfc3501/)