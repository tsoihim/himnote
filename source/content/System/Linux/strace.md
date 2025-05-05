> **strace** - trace system calls and signals
> 
> In the simplest case strace runs the specified command until it exits. It intercepts and records the system calls which are called by a process and the signals which are received by a process. The name of each system call, its arguments and its return value are printed on standard error or to the file specified with the -o option.

strace를 이용하면 프로세스가 발생시킨 시스템 콜이나 받은 시그널 등을 확인 가능

### Options
- -p
    - 이미 실행 중인 프로세스에 attach
- -i
    - syscall 발생시킨 instruction 주소 출력
- -T
    - syscall에서 소모한 시간 출력
- -c
    - 시간 내 발생한 call들에 대한 summary를 출력

여러가지 필터링 옵션을 통하여 원하는 것만 trace하는 것도 가능함

아래는 특정 프로세스(모든 fork 포함)의 network 관련 시스템콜(snd, rcv)을 출력하는 예제
```bash
root@nara_dev:obelle strace -fp 8279 -e trace=network
strace: Process 8279 attached with 12 threads
[pid  8292] recvfrom(141, "\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\25\\0\\0\\0\\37\\0\\0\\0\\7\\0\\0\\0\\2\\0\\0\\0,\\0\\0\\0"..., 1048576, 0, NULL, NULL) = 44
[pid  8292] recvfrom(141, "\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\25\\0\\0\\0\\36\\0\\0\\0\\7\\0\\0\\0\\0\\0\\0\\0(\\0\\0\\0"..., 1048576, 0, NULL, NULL) = 40
[pid  8292] recvfrom(141, "\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\25\\0\\0\\0\\37\\0\\0\\0\\7\\0\\0\\0\\2\\0\\0\\0,\\0\\0\\0"..., 1048576, 0, NULL, NULL) = 44
```