> **lsof** - list open files

lsof는 프로세스가 열고 있는 파일 목록을 출력

### Options
- p \[PID\]
    - 특정 프로세스가 열고 있는 파일 목록 출력
- i \[ini\]
    - 인터넷 어드레스가 ini에 매칭되는 파일 목록 출력
    - ex. lsof -i “:19900”
        - 19900 포트 쓰는 목록 출력