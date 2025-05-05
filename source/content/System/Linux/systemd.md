Systemd란 linux의 시스템 및 서비스 매니저. 1번 프로세스로 실행되어 유저공간의 서비스를 관리함 (like init)

systemctl 명령어를 통하여 system 및 서비스 매니저를 제어할 수 있으며(서비스 실행 등), journalctl 명령어를 통하여 system 및 서비스 매니저 로그(서비스별 로그 등)를 확인할 수 있음
- systemd는 로그 데이터를 journal이라는 바이너리 형식으로 저장
![[Pasted image 20250505125129.png|500]]

# Unit
Systemd는 12가지 타입(Service, Socket, Target, Device, etc.)의 unit이라고 불리는 엔티티들간의 의존관계 시스템을 제공

unit 설정 파일을 통하여 상기 타입의 unit들에 대한 설정을 작성, systemd가 관리하도록 할 수 있음

unit 설정 파일은 여러 섹션으로 이루어지며 섹션은 unit 타입에 의존적이지 않은 일반적인 정보에 대한 섹션과 unit 타입에 의존적인 정보에 대한 섹션으로 나뉨

일반적인 unit 정보에 대한 섹션은 아래 두가지가 있음
- Unit: unit 타입에 의존적이지 않은 일반적인 unit 정보
- Install: unit install 관련 정보. 런타임과 무관하며 systemctl의 enable/disable 커맨드가 unit 설치 및 삭제 시 사용

**Unit Section Options**
- Description: 서비스에 대한 설명
- Requires: 본 유닛이 의존성을 가지는 다른 유닛들의 리스트. (이 유닛이 활성화되면 여기에 리스팅한 다른 유닛들도 활성화됨)
- Requisite: Requires와 대동소이. 의존하는 다른 유닛이 비활성화되어 있으면 fail 반환
- Wants: Requires의 약한 버전. 의존하는 다른 유닛이 활성화되는데 실패해도 무시
- Before/After: Before 설정한 유닛이 시작되기 이전에 본 유닛이 먼저 실행됨. After는 이의 역.

**Install Section Options**
- WantedBy/RequiredBy: systemctl enable 커맨드로 본 유닛 설치 시 .wants/ 또는 .requires/ 디렉토리에 심볼릭링크가 생성되며 효과는 unit section의 wants 또는 requires와 같음
- Also: 본 유닛이 설치/삭제될 때 같이 설치/삭제될 유닛을 정의. systecmctl enable/disable 커맨드로 설치될 때 이 옵션에 지정한 유닛도 같이 설치/삭제 됨

## Service Unit
Service unit은 daemon과 그에 따르는 프로세스를 실행 및 관리하는 unit
Service unit의 설정은 .service 파일에 작성하며, 어떻게 systemd에 의해 관리될 지에 대한 내용을 작성

**Service Section Options**
- Type: service unit의 프로세스 start-up 타입 (*크게 두가지만 정리)
    - simple: ExecStart로 설정된 프로세스가 서비스의 메인 프로세스. 해당 프로세스가 시스템 내 다른 프로세스와 커넥션 필요한 경우 본 유닛 실행 전에 다른 프로세스와의 통신 채널이 미리 설치되어야함
    - forking: ExecStart로 설정된 프로세스가 fork 통해 서비스 start-up 하는 경우. 부모 프로세스는 start-up이 끝나는 대로 종료, 자식 프로세스들이 데몬 프로세스로서 계속 실행됨. 이 옵션 쓰는 경우 PIDFile 옵션 사용하여 systemd가 데몬의 메인 프로세스를 식별할 수 있도록 하는 것이 권장됨
- GuessMainPid: type 옵션이 forking인데 PIDFile 옵션 설정이 없는 경우 디폴트로 작동. 메인 프로세스의 pid를 찾는 로직이 작은 확률로 미스가 날 수 있다는 점에 유의
- PIDFile: 서비스의 PID File에 대한 절대경로를 설정. 서비스 매니저는 서비스의 메인 프로세스를 해당 파일을 통해 식별하게 됨
- ExecStart: 서비스 시작 시에 수행할 커맨드
- ExecStartPre/ExecStartPost: 서비스 시작 전/후에 수행할 커맨드
- ExecReload: 서비스 설정 재로드할 경우 수행할 커맨드. 환경 변수 재설정 등의 작업이 가능
- ExecStop: 서비스 중단 시에 수행할 커맨드. 디폴트로 kill 시그널을 프로세스에 전송
- ExexStopPost: 서비스 중단 후에 수행할 커맨드
- RestartSec: 서비스 재시작 전에 sleep할 시간을 결정
- TimeoutStopSec: 지정된 timeout 내에 서비스가 종료안되면 강제로 SIGTERM 보내는 설정
- Restart: 서비스 프로세스 종료 시 자동 재시작 여부 (*systemd로 종료시킨 경우는 제외, 일부만 정리)
    - on-failure: 프로세스가 non-zero 값으로 종료되거나 시그널 맞아서 종료된 경우 재시작
    - on-abnormal: 프로세스가 시그널 맞아서 종료된 경우 재시작
    - on-abort: 프로세스가 감지 못한 시그널로 인해 clean exit 하지 못한 경우 재시작
    - always: 언제나 재시작
    - no: 디폴트값. 재시작 안함

이외에 설정 파일 작성 시 Environment 구문을 통하여 환경 변수를 서비스 프로세스에 넘겨주는 것도 가능하며 WorkingDirectory도 지정할 수 있음

# Practice
기존의 운영 중인 서버 프로그램을 리눅스 서비스로 등록하여 재부팅 시 auto-restart 기능을 적용
```bash
[root@localhost system]# pwd
/etc/systemd/system
[root@localhost system]# cat test.service
[Unit]
Description=test program
After=network.target

[Service]
Type=forking
WorkingDirectory=/root/dist/test/bin
Environment=TEST_HOME=/root/dist/test/
Environment=LD_LIBRARY_PATH=/root/dist/test/lib
ExecStart=/root/dist/test/bin/test-service.sh start
ExecStop=/root/dist/test/bin/test-service.sh stop
RestartSec=10
TimeoutStopSec=300
TimeoutStopSec=60

[Install]
WantedBy=multi-user.target
```
- 서버 프로그램이 bash 의존적인 환경변수(ex. underscore)를 여럿 사용 중이라 미리 정의한 환경변수를 넘기는 것 뿐만 아니라 start 스크립트 통해 바이너리가 실행되도록 하였음
- 서비스 시작/중단 시에 타임아웃 지정하여 해당 시간 내에 성공하지 못하면 실패로 간주하도록 설정
- 부팅 시에 자동 시작을 위하여 Install 섹션의 WantedBy 옵션에 multi-user 타겟을 설정
    - 이를 통해 multi-user 런 레벨에서 본 서비스가 자동 실행될 수 있도록 함
    > 런 레벨이란 Unix 기반 OS의 operating state로 0 부터 6단계까지 존재함(runlevel 명령으로 확인 가능). 런 레벨은 부팅되고 나서 어떤 프로그램이 실행될 수 있는 지 결정. 필요에 따라 시스템의 기본 런 레벨을 정할 수 있으며 보통 서버 프로그램은 3단계인 multi-user(with networking)을 사용.
- Restart=on-failure 세팅을 통해 서비스가 시그널 등으로 죽었을 때도 restart 하도록 세팅하려했으나 일단은 부팅 시에 자동실행만 설정
```bash
[root@localhost system]# systemctl daemon-reload
[root@localhost system]# systemctl enable test
[root@localhost system]# systemctl start test
[root@localhost system]# systemctl status test
● test.service - Test Service
   Loaded: loaded (/etc/systemd/system/test.service; enabled; vendor preset: disabled)
   Active: active (running) since 수 2022-10-19 06:06:37 EDT; 3s ago
  Process: 3914 ExecStart=/root/dist/test/bin/start.sh (code=exited, status=0/SUCCESS)
 Main PID: 3916 (boot)
   CGroup: /system.slice/test.service
           ├─3916 /root/dist/test/bin/boot
           └─3919 repo -w 6000
```

### 참고
- 서비스 유닛 완전 삭제
```bash
systemctl stop [servicename]
systemctl disable [servicename]
rm /etc/systemd/system/[servicename]
rm /etc/systemd/system/[servicename] # and symlinks that might be related
rm /usr/lib/systemd/system/[servicename] 
rm /usr/lib/systemd/system/[servicename] # and symlinks that might be related
systemctl daemon-reload
systemctl reset-failed
```