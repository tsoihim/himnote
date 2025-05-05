> **cron** - daemon to execute scheduled commands cron is started automatically from /etc/init.d on entering multi-user runlevels.
> 
> **crontab** - maintains crontab files for individual users Crontab is the program used to install a crontab table file, remove or list the existing tables used to serve the cron(8) daemon.

cron을 이용하면 주기적으로나 특정 시간에 원하는 일을 서버에서 수행 가능
crontab 명령을 이용하면 실행할 내용을 담은 crontab 파일을 쉽게 관리할 수 있다

### Usage
- crontab -e : 크론탭을 설정할 수 있는 창이 열림
- crontab -l : 크론탭 내용 확인
- crontab -r : 크론탭 내용 삭제
- crontab [FILENAME] : 크론탭 내용 정의한 파일로 크론탭 내용 바꿔치기

### Cron Format
![[Pasted image 20250505130636.png|450]]

### Examples
```bash
[root@access1 ~]# crontab -l
0 5 * * * /root/scripts/backup.sh # 5시 0분 마다 실행
*/2 * * * * /root/scripts/backup.sh # 2분마다 실행 
```