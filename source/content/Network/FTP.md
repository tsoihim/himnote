## File Transfer Protocol (FTP)
- 평문 사용
### CMD
- open : FTP 서버 접속
- close : 접속 중인 연결 종료
- ls : 리눅스의 ls와 동일
    - local의 경우 lls
- pwd : 리눅스의 pwd와 동일
    - local의 경우 lpwd
- cd : 리눅스의 cd와 동일
    - local의 경우 lcd
- get : 파일 전송 받기
- put : 파일 올리기

- 그외 리눅스에서 파일 조작 관련한 명령어들은 다 쓸 수 있고 로컬의 경우 명령 앞에 l을 붙이면 됨
### FTP Client
Mac에 ncftp 설치하여 사용
```bash
Usage:  ncftp [flags] [<host> | <directory URL to browse>]

Flags:
  -u XX  Use username XX instead of anonymous.
  -p XX  Use password XX with the username.
  -P XX  Use port number XX instead of the default FTP service port (21).
  -j XX  Use account XX with the username (rarely needed).
  -F     Dump a sample $HOME/.ncftp/firewall prefs file to stdout and exit.

him@nara-MacBook:~ ncftp -u him [FTP 서버 IP 주소]
NcFTP 3.2.6 (Dec 04, 2016) by Mike Gleason (<http://www.NcFTP.com/contact/>).
Connecting to [FTP 서버 IP 주소]...
(vsFTPd 3.0.2)
Logging in...
Password requested by [FTP 서버 IP 주소] for user "him".

    Please specify the password.

Password: ********

Login successful.
Logged in to [FTP 서버 IP 주소].

ncftp /home/atto > ?
ascii      cat        get        lookup     mkdir      pwd        set
bgget      cd         help       lpage      open       quit       show
bgput      chmod      jobs       lpwd       page       quote      site
bgstart    close      lcd        lrename    passive    rename     type
binary     debug      lchmod     lrm        pdir       rhelp      umask
bookmark   dir        lls        lrmdir     pls        rm         version
bookmarks  edit       lmkdir     ls         put        rmdir
```

## SSH File Transfer Protocol (SFTP)
- FTP와는 관계없음
- SSH 사용한 독자적인 파일 전송 프로토콜
- vsftpd 등의 프로그램 필요 X (openssh-server 설치 후 세팅만 해도 사용 가능)