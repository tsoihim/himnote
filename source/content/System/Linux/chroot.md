> **chroot** - run command or interactive shell with special root directory
> 
> Usage : **** chroot [_OPTION_] _NEWROOT_ [ _COMMAND_ [ _ARG_]...] Run COMMAND with root directory set to NEWROOT

리눅스 환경에서는 chroot를 통하여 실행할 커맨드의 root 디렉토리를 직접 지정해줄 수 있음
- 루트 디렉토리 : 유닉스 계열 운영 체제에서 사용되는 개념으로, 파일 시스템에서 최상위 디렉토리를 가리킴 (트리의 root 노드와도 같음)

chroot를 실행할 때는 COMMAND 실행에 필요한 시스템 내의 다른 파일을 참조하지 못하는 경우를 유의해야함
- NEWROOT가 루트가 되었으므로 COMMAND로 실행되는 프로세스는 NEWROOT의 상위디렉토리에 접근이 불가하기 때문 ex) /etc 혹은 /usr 등의 디렉토리에 접근이 불가해질 수 있음

그렇다면 chroot는 왜 사용하는가?
- 상위디렉토리에 접근하지 못한다는 것은 반대로 생각해보면 접근을 막는 것이됨
    - 즉, COMMAND가 격리된 환경에서 실행되도록 만들 수 있음 (매우 심플한 프로세스 격리 방법)