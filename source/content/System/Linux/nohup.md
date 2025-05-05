> **NAME** nohup – invoke a utility immune to hangups
> 
> **DESCRIPTION** The nohup utility invokes utility with its arguments and at this time sets the signal SIGHUP to be ignored. If the standard output is a terminal, the standard output is appended to the file nohup.out in the current directory. If standard error is a terminal, it is directed to the same place as the standard output.

nohup 명령을 통해 터미널의 세션 연결이 끊기더라도 프로세스를 계속해서 동작시킬 수 있음
- 세션의 logout이 발생하면, 해당 터미널에서 실행된 프로세스들은 HUP 신호를 받고 종료됨
- nohup은 프로세스들이 이러한 시그널에 면역이 되도록 만들어주는 것

### 예제
nohup \[프로그램 or 스크립트 등\]
- 실행 파일 권한은 755 이상
- 백그라운드로 실행시키는 옵션인 &를 뒤에 붙여 nohup + background로도 실행시킬 수 있음
    - `nohup sh -c "while true; do echo -e 'HTTP/1.0 200 OK\\r\\n\\r\\nserver' | sudo nc -l -p 80 ; done" &`
        - 간단한 웹서버 실행