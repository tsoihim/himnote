> **Sysctl** - 런타임에 리눅스 커널 파라미터를 변경할 수 있는 명령어. 커널 파라미터 읽기/쓰기 모두 가능하며, 변경 가능한 파라미터는 /proc/sys 디렉토리에서 확인할 수 있음
> 
> Usage : **** sysctl [ _OPTIONS_ ] [ _VARIABLE_ [ =VALUE ]] […]

### 사용법 상세
VARIABLE이란 읽어들일 key 값 (ex. net.core~) 
VARIABLE을 특정 VALUE 값으로 변경하고자 하는 경우 w 옵션 주고 key에 VALUE를 대입
- 예시 1
    - 서버의 관리 노드가 많아짐에 따라 소켓 버퍼가 부족해지는 경우가 발생, 아래 명령어로 용량 확장
    ```bash
    # snd/rcv 버퍼 default 크기 확인
    root@sdnacc:~/# sysctl net.core.wmem_default
    net.core.wmem_default = 212992
    root@sdnacc:~/# sysctl net.core.rmem_default
    net.core.rmem_default = 212992
    
    # snd/rcv 버퍼 default 크기 확장
    root@sdnacc:~/# sysctl -w net.core.wmem_default=[VALUE]
    root@sdnacc:~/# sysctl -w net.core.rmem_default=[VALUE]
    ```
/etc/sysctl.conf에 설정하고자 하는 값을 넣고 sysctl p 옵션 활용해 파라미터 설정도 가능
- 예시 2
    - 공유 메모리 관련 설정 적용 후 확인
    ```bash
    # Test settings
    kernel.shmmni = 4096
    kernel.shmall = 2097152
    kernel.shmmax = 2147483648
    
    # 위와 같이 /etc/sysctl.conf 파일에 설정 추가
    
    # 파라미터 설정 적용
    root@nara_dev:~ sysctl -p
    kernel.shmmni = 4096
    kernel.shmall = 2097152
    kernel.shmmax = 2147483648
    ```
    
- 네트워크 설정 팁
    - [https://meetup.nhncloud.com/posts/53](https://meetup.nhncloud.com/posts/53)