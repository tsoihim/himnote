> **ulimit** - get and set user limits

대용량 네트워크 시뮬레이션 도중 파이썬 스크립트 프로세스가 뻗어서 확인해보니 한 프로세스가 열 수 있는 최대 파일 수 제한을 넘어서서 exception이 발생하였음

따라서 ulimit을 통하여 아래와 같이 user 제한 정보를 확인 (-a 옵션으로 모든 제한 정보 확인 가능)
```bash
root@nara_dev:~ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 31862
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 31862
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

현재 1024인 open files 값을 바꾸기 위하여 /etc/security/limits.conf 파일을 아래와 같이 직접 변경하였음 (ulimit [OPTION] [VALUE] 로도 수정 가능)
```bash
 43 #<domain>      <type>  <item>         <value>
 44 #*               soft    core            0
 45 #root            hard    core            100000
 46 #*               hard    rss             10000
 47 #@student        hard    nproc           20
 48 #@faculty        soft    nproc           20
 49
 50 root             soft    nofile          65536
```

이후 쉘 종료 후 재접속하여 다시 테스트하였을 때는 발생했던 문제가 재현되지 않는 것을 확인함