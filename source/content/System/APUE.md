<aside> 📔 Advanced Programming in the UNIX Environment를 읽고

</aside>

## **14. 고급 입출력**

### 14-3. 레코드 잠금
- 프로세스가 파일의 한 부분을 읽거나 쓰는 동안 다른 프로세스가 그 부분을 수정 못하게 하는 기능
    - flock 같은 함수도 있지만 레코드 잠금과는 달리 파일 전체를 잠금
- fcntl 함수로 레코드 잠금을 수행할 수 있음
- msgctl 함수로 아래의 명령 수행 가능
```c
#include <fcntl.h>

int fcntl(int fd, int cmd, ... /* struct flock *flockptr */ ); 
	Returns: depends on cmd if OK (see following), −1 on error
```
fcntl이 추가적으로 할 수 있는 일 정리
### 14-4. 입출력 다중화 (추가 정리)
- 폴링 방식
    - ?
select & epoll, level & edge trigger

## **15. 프로세스 간 통신**
### 15-2. 파이프
- 가장 오래된 UNIX 시스템 IPC로 모든 UNIX 시스템이 제공
- 파이프의 두가지 제약
    - half 및 full duplex 모두 지원하나 최대 이식성 위해서 half duplex를 권장
    - 부모가 같은 프로세스끼리만 사용가능. 보통 부모 자식 간 통신 위해 많이 씀
- 파이프 적용 예
    - 유닉스 셸에 명령들을 파이프로 이으면 선행하는 명령 프로세스의 표준출력을 파이프 이용하여 다음 프로세스 표준입력에 연결함
- 프로그램에서 파이프 호출 예제 및 도해
```c
#include <unistd.h>

int pipe(int fd[2]);
// fd 배열을 생성 후 pipe 함수로 넘기면 두 개의 fd를 할당 후 그 결과를 반환

/*
fd[0]과 fd[1]은 각각 읽기, 쓰기 전용; fd[1]의 출력이 fd[0]에 입력되는 것
부모-자식 간 파이프를 연결하면 아래 그림과 같은 형태가 되며, 데이터 흐르는 방향에 따라 해당하는 서술자를 닫으면 됨
*/
```
![[Pasted image 20250501200634.png|300]]
- 한쪽 끝이 닫힌 파이프에 대한 규칙
    - 쓰기 식별자가 닫힌 파이프의 모든 자료를 read로 읽고 read 재호출하면 0 반환 (파일의 끝)
    - 읽기 식별자가 닫힌 파이프에 write 호출하면 SIGPIPE 시그널 발생
    *pathconf로 확인할 수 있는 PIPE_BUF 값에 의해 커널의 파이프 버퍼 크기가 결정됨. 여러 프로세스들이 PIPE_BUF개 이상의 바이트들을 wirte로 기록하면 프로세스의 자료들이 뒤섞여 기록될 위험 있음
### 15-3. popen과 pclose 함수
- fork와 pipe 조합은 일반적이므로 표준 입출력 라이브러리에 popen과 pclose가 정의되어 있음(pipe 생성, fork 호출, 식별자 close 등)
- 프로그램에서 popen 호출 예제 및 도해
```c
#include <stdio.h>

FILE *popen(const char *cmdstring, const char *type); 
// cmdstring으로 지정된 명령을 fork와 exec로 실행 후 표준 입출력 파일 포인터를 반환
// Returns: file pointer if OK, NULL on error

int pclose(FILE *fp);
// Returns: termination status of cmdstring, or −1 on error

// 아래 그림과 같이 인자에 따라 두가지 형태를 띌 수 있음
```
![[Pasted image 20250501200738.png|500]]
- pclose 함수는 표출 입출력 스트림을 닫고, 명령 종료를 기다린 후 셸의 종지 상태 반환
### 15-4. 협동 프로세스
- UNIX 시스템에서 필터란 표준 입력을 읽고 표준 출력에 기록하는 프로그램
- 필터의 입력과 출력이 동일한 프로그램에 연결된 경우 필터는 협동 프로세스가 됨
- 즉, 협동 프로세스에서는 다른 프로세스와 단방향 파이프 두 개가 만들어짐
    *하나는 협동 프로세스의 표준 입력과 연결되고, 나머지는 표준 출력과 연결
- 다른 프로세스는 데이터를 협동 프로세스에 입력하고 협동 프로세스가 그것을 변환한 결과를 협동 프로세스의 출력에서 읽어들이는 식으로 작동
	![[Pasted image 20250501201215.png|500]]
### 15-5. FIFO
- FIFO는 named pipe라고도 불림, FIFO 이용하면 무관한 프로세스끼리 데이터 송수신이 가능
- fifo의 주요 용도
    - 셸 파이프라인에서 명령 간 자료 넘길 때 fifo 쓰면 임시파일 생성할 필요없음 (pipe로도 되지만 프로세스 간 직렬 연결만 가능)
    - 클라이언트와 서버가 자료 주고받는 경우
- fifo는 일종의 파일이며 아래 두 함수 통해 fifo 생성, open()하면 보통의 파일처럼 사용 가능
```c
#include <sys/stat.h>

int mkfifo(const char *path, mode_t mode); 
int mkfifoat(int fd, const char *path, mode_t mode); 
	Both return: 0 if OK, −1 on erro
```
- open으로 fifo 열 때 행동은 O_NONBLOCK 플래그 설정 여부에 따라 달라짐
    - 보통 지정하지 않음. 이 경우 읽기 모드로 fife열면 다른 프로세스가 해당 fifo를 쓰기용으로 열기 전까지 호출 차단됨. 반대도 마찬가지임.
    - 지정한 경우 읽기 모드로 열면 바로 반환됨. 쓰기 모드로 열면 읽기 모드로 해당 fifo를 연 프로세스가 없으면 에러 반환
- pipe와 같이 읽기 용으로 연 프로세스가 없는 fifo에 쓰면 sigpipe 전달되며, 한 fifo에 대한 마지막 기록자가 fifo 닫으면 읽는 프로세스는 eof 만남
- 여러 기록자가 있을 수 있으므로 한 번에 쓰는 양이 PIPE_BUF보다 작으면 원자적으로 쓰기 수행됨
### 15-6. XSI IPC
- 메시지 대기열, 세마포, 공유 메모리를 이용하는 형태의 IPC에 대한 통칭 (각각은 비슷한 점을 공유)
- 식별자와 키 (identifier and key)
    - 커널은 IPC 객체를 음이 아닌 정수 식별자를 통해 참조 (내부 식별자)
    - 외부에서 접근하기 위한 식별자는 키라고 하며 객체 생성 시 키를 반드시 지정해야함
    - 키는 sys/types.h에 key_t 로 정의. 보통 긴 정수이며 커널은 이 키를 식별자로 변환해 사용
- 클라이언트와 서버는 여러 방식으로 IPC 객체에서 만날 수 있음
    - 결국 식별자나 키를 공유하면 됨
    - 물론 키가 이미 점유되었을 수 있으므로 IPC_CREAT, IPC_EXCL 플래그 사용 가능
- XSI IPC 생성 함수는 같은 인수를 두개 사용함 (key, flag)
    - flag에 IPC_CREAT 비트를 켜면 IPC_PRIVATE을 지정했거나 기존 IPC 객체 중 주어진 key를 점유하는 것이 없으면 새 IPC 객체를 생성
    - 클라이언트 등에서 기존 IPC 객체 참고할거면 key를 인자로 넘기고 CREAT 비트는 꺼야됨
    - IPC_PRIVATE 지정하면 늘 새 IPC 객체 생성됨. PRIVATE으로 생성된 객체는 해당 객체 식별자를 알아낸다음 get 함수가 아닌 다른 IPC 함수 사용해야함
- 접근 권한 구조체
    - 각 IPC 객체마다 ipc_perm이라는 구조체가 연관되며 아래와 같은 구조를 가짐
    - 접근 권한 구조체는 IPC 객체 생성 시 초기화되며 IPC 객체 제어함수로 필드 수정, 단 호출 프로세스가 객체의 생성자이거나 슈퍼사용자여야함
    ```c
    struct ipc_perm { // in sys/ipc.h
    	uid_t uid; /* owner’s effective user ID */ 
    	gid_t gid; /* owner’s effective group ID */ 
    	uid_t cuid; /* creator’s effective user ID */ 
    	gid_t cgid; /* creator’s effective group ID */ 
    	mode_t mode; /* access modes */
    	…
    };
    ```
- XSI IPC의 장단점
    - 시스템 전역 객체이며 참조 횟수가 관리되지 않음. 파이프나 fifo는 프로세스가 종료되면 데이터가 제거되나 XSI IPC 객체들은 명시적으로 삭제안하면 시스템 재시동 전까지 유지됨
    - 파일 시스템 안의 이름을 통해 접근할 수 없으며 fd를 쓰지 않으므로 multiplex 적용 불가
    - 흐름 제어 가능; 자원 부족이나 수신자가 메시지 더 못 받으면 송신자가 sleep 후 가능할 때 자동으로 깨어남
### 15-7. 메시지 대기열
- 메시지들이 연결된 목록 형태로 커널 안에 저장된 것
- 각 메시지 대기열은 고유 식별자(대기열 id)로 구분
- 각 대기열마다 아래의 관련 구조체를 가짐 (대기열 생성 시 초기화)
```c
struct msqid_ds { // in sys/msg.h
	struct ipc_perm msg_perm; /* 접근권한 구조체 */
	msgqnum_t msg_qnum; /* # of messages on queue */ 
	msglen_t msg_qbytes; /* max # of bytes on queue */ 
	pid_t msg_lspid; /* pid of last msgsnd() */ 
	pid_t msg_lrpid; /* pid of last msgrcv() */ 
	time_t msg_stime; /* last-msgsnd() time */ 
	time_t msg_rtime; /* last-msgrcv() time */ 
	time_t msg_ctime; /* last-change time */ 
	…
};
```
- msgget 함수로 기존 대기열 혹은 새 대기열을 연다 (음이 아닌 대기열 id를 반환)
```c
int msgget(key_t key, int flag); 
	Returns: message queue ID if OK, −1 on error
```
- msgsnd로 대기열에 메시지를 추가
    - 메시지는 메시지 종류, 길이, 자료 바이트로 구성되고 msgsnd에 이를 인수로 넘겨야함
    - 메시지는 항상 대기열 끝에 추가됨
```c
int msgsnd(int msqid, const void *ptr, size_t nbytes, int flag); 
	Returns: 0 if OK, −1 on error
// ptr 인수는 메시지 버퍼를 가리키며 버퍼는 아래와 같은 형태 
struct mymesg { 
	long mtype; /* positive message type */ 
	char mtext[n]; /* message data, of length nbytes */ 
};

// IPC_NOWAIT flag 설정하면 send시 흐름 제어용 wait으로 안빠지고 바로 오류 반환 가능
```
- msgrcv로 대기열의 메시지를 가져옴
    - 꼭 선입선출로 가져올 필요는 없음. 종류별로 가져오는 것도 가능 (mtype 필드 이용)
    - ptr 인수는 메시지 종류와 실제 데이터 포함한 버퍼의 시작 위치 가리킴
    - nbytes로 메시지 길이 지정, 실제 메시지가 nbytes 보다 큰 경우 MSG_NOERROR flag 세팅하여 끝이 잘린 메시지도 수신 가능
    - type 인수로 가져올 메시지 종류 설정 (0이면 첫 메시지, 양수면 특정 메시지 종류 전체, 음수면 type 절대값보다 작거나 같은 첫 메시지)
    - snd와 마찬가지로 IPC_NOWAIT 플래그 설정해 비차단 방식으로 recv 가능
```c
ssize_t msgrcv(int msqid, void *ptr, size_t nbytes, long type, int flag);
```
- msgctl 함수로 아래의 명령 수행 가능
    - IPC_STAT : 대기열에 대한 구조체를 buf 인수가 가리키는 곳에 저장   
    - IPC_SET : buf 인수가 가리키는 구조체의 필드를 대기열 구조체 필드에 복사
    - IPC_RMID : 시스템에서 대기열에 남아 있는 모든 자료 제거 (생성자 및 슈퍼사용자용)
        파일은 fd가 닫히기 전까지 내용이 안지워지나 XSI IPC 계열은 그냥 삭제됨(ungraceful)
```c
#include <sys/msg.h>
int msgctl(int msqid, int cmd, struct msqid_ds *buf ); 
	Returns: 0 if OK, −1 on error
```
- 전이중 파이프 등에 비해 큰 메리트 없으므로 자주 사용할 이유가 마땅치 않음
### 15-8. 세마포
- 여러 프로세스의 공유 자원 접근을 제어하기 위한 카운터, 이진 세마포로 mutex처럼 사용도 가능
- 프로세스가 공유 자원을 얻는 과정
    1. 자원을 제어하는 세마포 값을 점검
    2. 값이 양수이면 자원 사용 가능, 세마포 값을 1 감소시킴
    3. 값이 음수이면 세마포 값이 양수가 될 때까지 슬립, 깨어나면 단계 1로 돌아감
- 이러한 세마포의 동작 과정으로 인하여 세마포 값 점검/감소 등은 원자적으로 수행돼야 함 (이로 인해 보통 커널에서 구현됨)
- XSI 세마포의 문제
    - 한 세마포는 하나 이상의 세마포 값의 집합 (복잡함)
    - 생성과 초기화가 분리되어있어 덜 원자적임
    - 자동으로 시스템에서 삭제안해줌
- 커널은 각 세마포마다 아래 구조체를 적용
```c
#include <sys/sem.h>
struct semid_ds { 
	struct ipc_perm sem_perm; /* 접근 권한 구조체 */
	unsigned short sem_nsems; /* # of semaphores in set */ 
	time_t sem_otime; /* last-semop() time */ 
	time_t sem_ctime; /* last-change time */
	…
};

struct { // 개별 세마포값 구조체 (최소 필드만 서술)
	unsigned short semval; /* semaphore value, always >= 0 */ 
	pid_t sempid; /* pid for last operation */ 
	unsigned short semncnt; /* # processes awaiting semval>curval */ 
	unsigned short semzcnt; /* # processes awaiting semval==0 */
	…
};
```
- semget 함수로 세마포 ID 획득 (생성인지 참조인지는 다른 XSI와 같음)
    - 새집합 생성 시에는 nsems를 반드시 지정, 참조 시에는 0으로 세팅
```c
int semget(key_t key, int nsems, int flag); 
	Returns: semaphore ID if OK, −1 on error
```
- semctl 함수로 세마포 제어 등 여러 명령 수행
    - 4번째 인수는 특정 cmd 타입을 위해 사용하는 인수(semun). 생략가능
    - SETVAL/SETALL로 세마포 설정하면 모든 프로세스에서 그 세마포 조정값이 0으로 설정됨
```c
int semctl(int semid, int semnum, int cmd, ... /* union semun arg */ );
	Returns: (밑에서 설명)

union semun { // union임을 주의
	int val; /* for SETVAL */ 
	struct semid_ds *buf; /* for IPC_STAT and IPC_SET */ 
	unsigned short *array; /* for GETALL and SETALL */ 
};

/* 아래는 cmd 타입
IPC_STAT - 세마포 집합의 semid_ds 구조체를 arg.buf가 가리키는 구조체에 저장
IPC_SET - arg.buf가 가리키는 구조체 필드를 세마포 집합 구조체에 복사 (생성자/슈퍼유저용)
IPC_RMID - 시스템에서 세마포 집합 제거 (생성자/슈퍼유저용)
GETVAL - 특정 세마포 멤버의 semval 값을 반환
SETVAL - 특정 세마포 멤버의 semval 값을 arg.val로 설정
GETPID - 특정 세마포 멤버의 sempid 값을 반환
GETNCNT - 특정 세마포 멤버의 semcnt 값을 반환
GETZCNT - 특정 세마포 멤버의 semzcnt 값을 반환
GETALL - 집합의 모든 세마포 값 조회, 해당 값을 arg.array가 가리키는 배열에 저장
SETALL - 모든 세마포 값을 arg.array가 가리키는 값들로 설정
// read 관련 명령 외에는 성공 시 0, 실패 시 -1 반환
*/
```
- semop 함수는 세마포 집합에 대한 일련의 연산들을 원자적으로 수행 (즉, 배열의 모든 연산이 수행되거나 하나도 수행되지 않음)
    - semoparray 인수는 세마포 연산들의 배열, 각 세마포 연산은 위 sembuf 구조체로 대표됨
    - nops 인수에는 배열의 연산 개수(원소 개수)를 지정
```c
int semop(int semid, struct sembuf semoparray[], size_t nops); 
	Returns: 0 if OK, −1 on error

struct sembuf { 
	unsigned short sem_num; /* member # in set (0, 1, ..., nsems-1) */ 
	short sem_op; /* operation (negative, 0, or positive) */ 
	short sem_flg; /* IPC_NOWAIT, SEM_UNDO */ 
};

/*
sembuf 구조체의 sem_op 필드는 각 세마포에 적용할 연산
- sem_op가 양수이면 프로세스가 자원을 돌려줌, sem_op값이 세마포 값에 더해짐, SEM_UNDO 플래그가 세팅되면 이 프로세스에 대한 세마포 조정 값이 sem_op만큼 감소
- sem_op가 음수이면 프로세스가 자원을 획득하며 세마포 값이 sem_op 값만큼 감소 (세마포 값이 sem_op의 절대값보다 크거나 같아여 가능) SEM_UNDO 세팅할 경우 프로세스에 대한 세마포의 조정 값이 sem_op 절대값만큼 증가
		* IPC_NOWAIT를 지정한 경우 semop는 EAGAIN 오류 반환 가능
		* 그외엔 세마포 semncnt가 1 증가(호출자는 sleep 후 아래 조건 중 하나 대기)
			1. 세마포 값이 sem_op 절대값보다 크거나 같아짐, 이 경우 세마포의 semncnt가 1 감소하고 세마포 갓ㅂ이 sem_op 절대값만큼 감소
			2. 세마포가 시스템에서 제거되어 EIDRM 오류 반환
			3. 프로세스가 시그널 맞아서 핸들러가 반환됨. 이 경우 semncnt가 1 감소하며 semop는 EINTR 오류 반환
- sem_op가 0이면 호출 프로세스는 세마포 값이 0이 될 때까지 대기, 세마포 현재 값이 0이면 함수 즉시 반환되며 0이 아니면 아래 로직 수행
		* IPC_NOWAIT를 지정한 경우 semop는 EAGAIN 오류 반환 가능
		* 그외엔 세마포 semzcnt가 1증가(호출자는 sleep 후 아래 조건 중 하나 대기)
			1. 세마포 값이 0이 되고 semzcnt가 1 감소
			2. 세마포가 시스템에서 제거되어 EIDRM 오류 반환
			3. 프로세스가 시그널 맞아서 핸들러가 반환됨. 이 경우 semzcnt가 1 감소하며 semop는 EINTR 오류 반환
*/
```
- 세마포로 자원 획득 후 해제 안하고 그냥 프로세스 종료되면 문제 생김
    - UNDO 플래그 지정해 sem_op 사용하면 커널이 sem_op 절대값을 기억, 프로세스 종료 시 세마포를 조정해야할 값이 프로세스에 남아있는지 점검 후 필요 시 그 조정 값 세마포에 적용
### 15-9. 공유 메모리
- 여러 프로세스가 메모리의 한 영역(익명의 메모리 구역)을 공유하게 하는 기능 (따라서 매우 빠름)
- 여러 프로세스가 메모리에 접근하므로 동시성있는 자원 접근을 잘 제어해야함(보통 세마포로 제어)
- 커널은 공유 메모리 구역마다 아래 구조체를 유지

```c
#include <sys/shm.h>
	struct shmid_ds { // 최소 필드만 서술
	struct ipc_perm shm_perm; /* 접근 권한 구조체 */ 
	size_t shm_segsz; /* size of segment in bytes */ 
	pid_t shm_lpid; /* pid of last shmop() */ 
	pid_t shm_cpid; /* pid of creator */ 
	shmatt_t shm_nattch; /* number of current attaches */ 
	time_t shm_atime; /* last-attach time */ 
	time_t shm_dtime; /* last-detach time */ 
	time_t shm_ctime; /* last-change time */
	…
};
```
- shmget 함수로 공유 메모리 id 획득 (생성인지 참조인지 결정하는 방식은 다른 XSI IPC와 같음)
    - 생성 시 size는 무조건 설정해야하며 참조 시엔 0으로 지정
    - 실제 메모리 구역은 보통 size 인수의 크기를 시스템 페이지 크기의 정수배로 반올림한것 (따라서 마지막 패딩된 구역은 사용불가, 생성된 구역은 0으로 모두 초기화되어있음)
```c
int shmget(key_t key, size_t size, int flag); 
	Returns: shared memory ID if OK, −1 on error
```
- shmctl 함수로 아래의 명령 수행 가능
```c
int shmctl(int shmid, int cmd, struct shmid_ds *buf ); 
	Returns: 0 if OK, −1 on error

/* 아래는 cmd 타입
IPC_STAT - 구역에 대한 shmid_ds 구조체를 buf가 가리키는 구조체에 저장
IPC_SET - buf가 가리키는 구조체 필드를 구역의 shmid_ds 구조체에 복사(생성자/슈퍼유저용)
IPC_RMID - 시스템에서 공유 메모리 구역을 제거 (생성자/슈퍼유저용). 시스템은 구역의 부착횟수(shm_nattch)를 관리, 구역은 사용 프로세스 종료되거나 탈착되기 전까진 제거 안됨. 구역이 쓰이는지와는 별개로 구역의 식별자는 즉시 제거되며 해당 구역은 shmat로 다시 부착 불가
// 리눅스는 아래 둘도 지원
SHM_LOCK - 공유 메모리 구역을 잠금 (슈퍼유저용).
SHM_UNLOCK - 공유 메모리 구역을 잠금 해제 (슈퍼유저용).
*/
```
- shmat/shmdt 함수로 구역을 프로세스 주소 공간에 부착/탈착
    - 하드웨어 호환성을 위해 addr은 0으로 지정해주는 것이 좋음
    - flag에 SHM_RDONLY 세팅해서 읽기 전용 부착도 가능
    - 부착 성공 시 shm_nattch 필드 1 증가, 탈착은 그 반대
```c
void *shmat(int shmid, const void *addr, int flag); 
	Returns: pointer to shared memory segment if OK, −1 on error
/*
- addr이 0이면 커널이 선택한 부착 가능한 첫 주소에 부착 (권장)
- addr이 0이 아니고 SHM_RND 플래그 없으면 구역은 addr로 주어진 주소에 부착됨
*/

int shmdt(const void *addr); 
	Returns: 0 if OK, −1 on error
```
### 15-10. POSIX 세마포
- XSI 세마포의 여러 단점을 해결 (성능, 간결함, graceful)
    - 더 높은 성능
    - 세마포 집합 등의 개념이 없고, 루틴들이 파일 시스템 연산 패턴을 따르므로 간결
    - graceful한 삭제. 세마포가 제거돼도 마지막 참조가 해제될 때까진 연산들이 정상 작동
- 크게 named / unnamed 세마포로 나뉨 (생성/삭제 방식이 다르나 그 외에는 동일하게 작동)
    - named 세마포는 이름을 통해 접근, 어떤 프로세스도 접근 가능
    - unnamed 세마포는 메모리 안에 존재하며 해당 메모리에 접근 가능한 프로세스만 사용 가능
- 명명된 세마포 생성/참조 시 아래의 sem_open 함수 호출
    - 명명된 세마포 참조 시 인수 두개를 지정 (name에 이름, oflag에 0을 지정)
    - 생성 시 oflag에 O_CREAT 지정 (이름 중복되면 기존 세마포를 참조하게됨, O_EXCL 플래그를 설정하여 이 경우 실패 반환하도록 설정 가능)또한, mode(세마포 접근 권한, 한 번 지정 시 고정됨), value(세마포 초기값) 추가로 지정
```c
#include <semaphore.h>
sem_t *sem_open(const char *name, int oflag, ... /* mode_t mode, 
                unsigned int value */); 
	Returns: Pointer to semaphore if OK, SEM_FAILED on error
```
- 명명된 세마포 사용이 끝나면 sem_close 함수 호출하여 관련 자원 해제
    - 프로세스 sem_close 호출하지 않고 종료되면, 열려 있던 세마포를 커널이 자동으로 해제
    - 세마포 값에는 영향을 주지않음(UNDO가 없다)
```c
int sem_close(sem_t *sem); 
	Returns: 0 if OK, −1 on error
```
- 명명된 세마포를 완전히 제거하는 경우 sem_unlink 함수를 호출
    - 세마포 이름을 제거, 해당 세마포 참조하는 다른 프로세스가 없으면 세마포 자체를 파괴
```c
int sem_unlink(const char *name); 
	Returns: 0 if OK, −1 on error
```
- 무명 세마포 생성 시에는 sem_init을 호출
    - pshared가 0이 아니면 여러 스레드에서 세마포를 공유, vaule는 세마포 초기치
    - sem_open과 달리 sem 포인터에 초기화된 세마포 저장 후 함수 반환
```c
int sem_init(sem_t *sem, int pshared, unsigned int value);
	Returns: 0 if OK, −1 on error
```
- 무명 세마포 사용완료 후 제거 시에 sem_destroy를 호출
    - 제거 후에는 sem이 가리키는 세마포 사용 불가 (재사용 시 sem_init으로 재초기화해야함)
```c
int sem_destroy(sem_t *sem);
	Returns: 0 if OK, −1 on error
```
- 세마포 감소 시(자원 획득) 아래 wait 관련 함수 호출
    - 아래 두 함수 차이는 블로킹 여부 (trywait은 세마포가 0이면 즉시 -1 에러(EAGAIN) 리턴)
    - timedwait 함수로 블로킹 타임아웃 지정 가능, tsptr로 타임아웃 시점 지정 (타임아웃 시에 -1 에러(ETIMEDOUT) 리턴)
```c
#include <time.h>
int sem_wait(sem_t *sem); 
int sem_trywait(sem_t *sem); 
int sem_timedwait(sem_t *restrict sem, const struct timespec *restrict tsptr);
	All return: 0 if OK, −1 on error
```
- 세마포 해제 시(자원 해제) sem_post 함수 호출
    - 블로킹으로 wait 걸어놓은 프로세스가 있는 경우 post 호출하면 차단된 프로세스가 깨어남 (세마포가 1 증가했다가 바로 1 감소함)
```c
int sem_post(sem_t *sem);
	Returns: 0 if OK, −1 on error
```
- sem_getvalue로 세마포 조회는 가능하나 시점에 따라 값이 다르므로 디버깅용임

## **16. 네트워크 IPC: 소켓 (추가 정리)**
TCP 옵션 - reuseaddr, time_wait

## **17. 고급 IPC (추가 정리)**
도메인 소켓, fd 전달