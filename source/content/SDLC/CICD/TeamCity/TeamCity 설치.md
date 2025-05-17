## 개요
TeamCity 는 jetbrains에서 만든 CI/CD 툴로 유명한 jenkins와 비슷
jenkins 는 완전 무료이고 제약이 없는 대신, UI 가 매우 떨어지고 사용이 어려움
TeamCity는 유료 제품이라 jenkins의 단점 부분을 어느정도 해소한 것
단, professional 제품은 무료라 개인 사용자나 소규모 팀 정도에서는 무료로 사용이 가능함

TeamCity를 설치하는 여러 방법이 있는데, 여기서는 관리가 편한 docker 기반으로 설치함

# 설치
## Server 설치
우선 Server 설치가 필요하다.
여기서는 Ubuntu 서버 (ubuntu 18.04)에 설치하는 것을 기준으로 설명하고 있다.
https://www.jetbrains.com/teamcity/download/#section=docker 의 내용을 참고로 하면 된다.

```bash
docker run -it --name teamcity-server-instance \\
	-v /opt/teamcity/data:/data/teamcity_server/datadir \\
	-v /opt/teamcity/log:/opt/teamcity/logs \\
	-p 8000:8111 \\
	jetbrains/teamcity-server
```
docker 실행을 -it 옵션을 주어 실행했기 때문에 해당 터미널은 docker에 붙어 있게 된다. 빠져나오기 위해서는 CTRL + p, q 를 누르면 된다. (추후 해당 container에 들어가려면 docker exec bash 를 이용하면 된다.)

TeamCity 에서 사용할 Database가 필요하다. 
내장된 HSQLDB 를 이용해도 되지만, 추후 백업이나 이전 등을 고려하면 별도의 RDB를 이용하는 것이 나을 것 같아 mariadb 를 이용하기로 했다.
ubuntu 에서 mariadb 를 설치하기 위해 다음과 같이 하면 된다.
```bash
apt install mariadb-server mariadb-client
```

ubuntu 에서 mariadb 를 설치하면 기본으로 local 접속만 허용한다. 우리는 docker container 안에 teamcity 를 설치하였기 때문에 mariadb 가 외부 ip 를 이용하여 접속하게 된다. 이를 허용하기 위해 /etc/mysql/mariadb.conf.d/50-server.cnf 를 편집해야 한다.
```bash
# vi /etc/mysql/mariadb.conf.d/50-server.cnf
 
[server]
 
innodb_file_format=barracuda
innodb_large_prefix=on
 
[mysqld]
 
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
#bind-address           = 127.0.0.1
```
위의 예와 같이, mysqld 안에 있는 bind-address 부분을 주석 처리해 주면 된다.
그리고, TeamCity 에서 사용하는 index key 크기가 매우 큰데, mysql(mariadb)의 기본 설정으로는 이를 받을 수 없기 때문에 server 부분에 위와 같은 두 줄을 추가해 주어야 한다.

작업을 마쳤으면 mysql을 재시작하여 설정을 적용해 준다.

그리고 TeamCity에서 사용할 설정을 해 준다.
```bash
# mysql
 
MariaDB [(none)]> create database teamcity;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON teamcity.* \\
                  TO 'teamcity'@'localhost' IDENTIFIED BY 'atto1234';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON teamcity.* \\
                  TO 'teamcity'@'%' IDENTIFIED BY 'atto1234';
```
위와 같이 teamcity 라는 database를 만들어주고, teamcity 라는 계정을 만들어준다.

이제 Web으로 TeamCity 에 접속하여 초기 설정을 진행한다. 접속을 위해서는 docker를 띄울 때 사용했던 호스트쪽 port 정보를 이용하여 접속할 수 있다. 즉 여기서는 http://10.72.1.22:8000으로 접속하면 아래와 같은 화면이 나온다.
![[Pasted image 20250517153641.png|500]]

다음 버튼을 누르면 Database를 선택하는 화면이 나온다.
![[Pasted image 20250517153700.png|500]]

우리는 MySQL(mariadb) 를 이용하기로 했으니 이를 선택한다.
![[Pasted image 20250517153716.png|500]]
위와 같은 화면이 나오는데, 우선 JDBC 드라이버를 받고 (버튼을 누르고 좀 기다리면 된다.) 아래 부분을 채워주면 된다.
DB 관련 작업이 끝나면 초기화를 하고, 라이센스 동의 화면이 나온다. 해당 부분에 동의하고 넘어가면 계정 생성 화면으로 넘어간다.
![[Pasted image 20250517153753.png|500]]
계정 생성이 끝나면 설치가 완료된다.

## Agent 설치
실제 동작을 위해서는, 작업을 진행할 agent 들이 필요하게 된다.
무료 버젼인 Professional 에서는 최대 3개의 agent까지 설치가 가능하다. (우리는 소규모로 동작하기 때문에 1개의 agent만 사용한다.)
agent도 서버와 마찬가지로 docker 를 이용하기로 한다.
https://hub.docker.com/r/jetbrains/teamcity-agent%EC%97%90%EC%84%9C 에서 agent를 설치하는 방법을 볼 수 있다.

```bash
docker pull jetbrains/teamcity-agent
```
명령으로 agent 이미지를 받아온다.

```bash
docker run -it -e SERVER_URL="<url to TeamCity server>"  \\
    -v <path to agent config folder>:/data/teamcity_agent/conf  \\
    --name teamcity-agent jetbrains/teamcity-agent
```
명령으로 agent를 실행한다.

서버의 화면에서 agent 부분으로 가서 등록된 agent를 허가해 주면 등록이 완료된다.
하지만, 기본으로 설치된 agent는 우리가 원하는 go 환경이나 docker 환경 등이 부족하기 때문에, docker 이미지를 수정해 줄 필요가 있다.
```bash
docker exec -it teamcity-agent bash
```
명령으로 agent container 안으로 들어간 뒤, 원하는 환경을 구성해 준다. (go 설치 등등)

원하는 설정을 마쳤으면, exit 로 빠져나온 뒤 해당 이미지를 저장해준다.
```bash
docker stop teamcity-agent
docker commit teamcity-agent athene-teamcity-agent
docker rm teamcity-agent
```

등록된 이미지를 이용하여, path를 추가하여 다시 agent 를 띄워주자
```bash

docker run -it --name teamcity-agent -e SERVER_URL="10.72.1.22:8000" \\
    -v /opt/teamcity/agent:/data/teamcity_agent/conf \\
    -v /var/www/html/athene:/data/athene \\
    -v /var/run/docker.sock:/var/run/docker.sock \\
    athene-teamcity-agent
```
와 같이 띄우면 된다.
(teamcity_agent/conf 는 agent 설정을 위한 것, /data/athene 는 결과를 공유하기 위한 것, docker.sock은 docker 명령을 이용하기 위한 것이다.)