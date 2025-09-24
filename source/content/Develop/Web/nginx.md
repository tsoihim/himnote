### nginx란?
기본적으로 HTTP 웹 서버이며, 아래의 역할을 모두 수행할 수 있음
- 리버스 프록시([[Proxy]])
- 콘텐츠 캐시
- 로드 밸런서
- 메일 및 TCP/UDP 프록시 서버

현업에서 HTTP 웹 서버이자 리버스 프록시로 사용하고 여기에 더하여 정적 데이터 캐싱 및 로드 밸런스까지 사용하는 경우가 많음

### 주요 기능
- Proxy (HTTP, TCP, UDP, and MAIL)
- Load Balance
- Rate Limit (Request/Response)
- ACL
- Logging
- Content Caching
- Request Mirroring
- TLS Termination

### 설치
CentOS7 기준, 아래 repo를 추가
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1
```
이후 yum으로 설치 진행
- `sudo yum install -y nginx`

용도에 맞게 사용 포트 등 설정
- `sudo vi /etc/nginx/conf.d/default.conf`
- `nginx -t`로 설정 검사 가능
- 필요 시 방화벽 설정 해야함

서비스 등록 및 시작
- `systemctl enable nginx`
- `systemctl start nginx`

위 과정 정상적으로 마친 경우 설치한 서버의 IP 및 대상 포트로 접속하면 아래와 같은 화면 확인 가능
![[Pasted image 20250924154124.png|450]]

cli로는 nginx 명령어 사용
```
[user@localhost nginx]$ nginx -h
nginx version: nginx/1.26.1
Usage: nginx [-?hvVtTq] [-s signal] [-p prefix]
             [-e filename] [-c filename] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /etc/nginx/)
  -e filename   : set error log file (default: /var/log/nginx/error.log)
  -c filename   : set configuration file (default: /etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file
```

### 설정
`/etc/nginx/nginx.conf`에서 최상위 설정 파일 내용 확인 가능
`/etc/nginx/conf.d`에서는 하위 서비스별 설정 파일이 존재
- 최상위 설정 파일에 모든 설정 때려박아도 문제는 없음

기본적인 Revese Proxy 설정은 아래와 같이 정의
- 설정 기능
	- Reverse Proxy
	- TLS Termination
	- Load Balance
	- Rate Limit
	- Security
	- Logging
```
W/A
```


**References**
- https://nginx.org/en/