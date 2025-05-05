> **top** - The top program provides a dynamic real-time view of a running system. It can display system summary information as well as a list of processes or threads currently being managed by the Linux kernel.

### Usage
아래와 같이 top 입력하여 바로 실행 가능
```bash
atto@nara_dev:core top
top - 05:04:56 up 10 days, 19:44,  2 users,  load average: 0.13, 0.05, 0.04
Tasks: 180 total,   1 running, 179 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.0 us,  0.2 sy,  0.0 ni, 98.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  8174760 total,  3206816 free,   924400 used,  4043544 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  5272628 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
18835 atto      20   0   40516   3616   3056 R   6.2  0.0   0:00.01 top
    1 root      20   0   37484   4636   3056 S   0.0  0.1   0:06.54 systemd
    2 root      20   0       0      0      0 S   0.0  0.0   0:00.02 kthreadd
```

### Options
- -c : COMMANG full로 보기 가능

### CMD
| key       | function         |
| --------- | ---------------- |
| shift + m | memory usage로 정렬 |
| 1         | CPU별 사용량 확인      |
|           |                  |
