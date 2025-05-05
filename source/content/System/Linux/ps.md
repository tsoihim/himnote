> **ps** - report a snapshot of the current processes.
> 
> ”ps displays information about a selection of the active processes.”

ps 명령어를 통해 리눅스 시스템 상에서 실행 중인 프로세스 목록과 상태를 조회할 수 있음
반복적으로 업데이트 되는 프로세스 정보를 출력받고 싶으면 [top](https://www.notion.so/Top-8a6d16c3bf8a468d91281cd6284a46de?pvs=21)을 호출

### 예제
```
   
   To see every process on the system using standard syntax:
			ps -e
			ps -ef
			ps -eF
			ps -ely

   To see every process on the system using BSD syntax:
      ps ax
      ps axu

   To print a process tree:
      ps -ejH
      ps axjf
      ps axu

   To print a process tree:
      ps -ejH
      ps axjf

   To get info about threads:
      ps -eLf
      ps axms

   To get security info:
      ps -eo euser,ruser,suser,fuser,f,comm,label
      ps axZ
      ps -eM

   To see every process running as root (real & effective ID) in user
   format:
      ps -U root -u root u

   To see every process with a user-defined format:
      ps -eo pid,tid,class,rtprio,ni,pri,psr,pcpu,stat,wchan:14,comm
      ps axo stat,euid,ruid,tty,tpgid,sess,pgrp,ppid,pid,pcpu,comm
      ps -Ao pid,tt,user,fname,tmout,f,wchan

   Print only the process IDs of syslogd:
      ps -C syslogd -o pid=

   Print only the name of PID 42:
      ps -q 42 -o comm=
```

### PROCESS STATE CODES
```
Here are the different values that the s, stat and state output
specifiers (header "STAT" or "S") will display to describe the
state of a process:      

			     D    uninterruptible sleep (usually IO)
           R    running or runnable (on run queue)
           S    interruptible sleep (waiting for an event to
                complete)
           T    stopped by job control signal
           t    stopped by debugger during the tracing
           W    paging (not valid since the 2.6.xx kernel)
           X    dead (should never be seen)
           Z    defunct ("zombie") process, terminated but not reaped
                by its parent

   For BSD formats and when the stat keyword is used, additional
   characters may be displayed:

           <    high-priority (not nice to other users)
           N    low-priority (nice to other users)
           L    has pages locked into memory (for real-time and
                custom IO)
           s    is a session leader
           l    is multi-threaded (using CLONE_THREAD, like NPTL
                pthreads do)
           +    is in the foreground process group

```