## 개념
### SMTP란
단순 메일 전송 프로토콜의 약어로, 메일을 목적지로 보내고 릴레이하기 위하여 사용됨

### **ESMTP란**
확장 단순 전자우편 전송 프로토콜(ESMTP)은 기존 기능을 확장하여 이메일 첨부 파일 전송, TLS 사용, 기타 기능을 사용할 수 있게 해주는 프로토콜 버전

대부분의 이메일 클라이언트와 이메일 서비스는 기본 SMTP가 아닌 ESMTP를 사용

### **SMTP 작동 방식**
SMTP는 이메일 클라이언트와 메일 서버 간의 데이터 교환 프로세스를 정의

이메일 클라이언트와 전자 메일 서버 사이에서 이메일 전송이 시작되는 과정은 아래와 같음
- **SMTP 연결 열림**
    - 첫 번째 단계는 클라이언트와 서버 간 TCP 연결로 시작. 그 다음 이메일 클라이언트가 특화된 "Hello" 명령(HELO 또는 EHLO, 아래 설명됨)으로 이메일 전송 프로세스를 시작
- **이메일 데이터 전송**
    - 클라이언트가 이메일 헤더(대상 및 제목 줄 포함), 이메일 본문, 기타 추가 구성 요소로 이루어진 실제 이메일 콘텐츠와 함께 일련의 명령을 서버에 전송
- **메일 전송 에이전트(MTA)**
    - 서버가 메일 전송 에이전트(MTA)라는 프로그램을 실행. MTA는 수신자의 이메일 주소 도메인을 확인하고 발신자와 다를 경우 수신자의 IP 주소를 찾도록 도메인 네임 시스템(DNS)에 쿼리
- **연결 닫힘**
    - 데이터 전송이 완료되면 클라이언트가 서버에 알림을 보내며 서버가 연결을 닫음

일반적으로, 이 첫 번째 이메일 서버는 이메일의 실제 최종 목적지가 아니며 릴레이 될 수 있음

### **SMTP 봉투**
SMTP "봉투"는 이메일 클라이언트가 이메일의 출발지와 목적지에 관해 메일 서버에게 보내는 정보의 집합. SMTP 봉투는 이메일 헤더 및 본문과 구별되며 이메일 수신자에게는 보이지 않음

### **SMTP 명령**
SMTP 명령은 미리 정의된 텍스트 기반 지시사항으로, 클라이언트나 서버에서 수행해야 할 작업과 이에 수반된 데이터 처리에 대한 명세
- `HELO/EHLO`
    - 클라이언트와 서버 사이에 SMTP 연결 수립을 위한 명령
- `MAIL FROM`
    - 이메일을 전송하는 발신자 표기
        - Alice가 이메일을 보내는 경우 클라이언트가 "MAIL FROM:[alice@example.com](mailto:alice@example.com)"을 전송
- `RCPT TO`
    - 이 명령으로 이메일 수신자를 나열
- `DATA`
    - 이메일 콘텐츠 앞에 위치
- `RSET`
    - 이 명령을 사용하면 연결이 초기화되어 SMTP 연결이 닫히지 않고 미리 전송한 모든 정보 제거
    - 클라이언트가 잘못된 정보를 전송할 때 사용
- `QUIT`
    - 이 명령으로 연결 종료

### **SMTP 서버**
SMTP 서버는 SMTP 프로토콜을 사용해 이메일을 전송하고 수신할 수 있는 메일 서버

SMTP 서버에서 실행되는 각기 다른 소프트웨어 프로그램은 다음과 같음
- **메일 제출 에이전트(MSA)**
    - MSA는 이메일 클라이언트로부터 이메일을 수신
- **메일 전송 에이전트(MTA)**
    - MTA는 전달망의 다음 서버로 이메일을 전송
- **메일 전달 에이전트(MDA)**
    - MDA는 MTA에서 이메일을 수신해서 수신자의 받은메일함에 보관
- 보통 SSL의 경우 Port 465, TLS의 경우 587을 사용

### 참고
- **MIME**
    - 전자 우편을 위한 인터넷 표준 포맷
    - 전자 우편은 7비트 ASCII 문자를 사용하므로 8비트 이상의 코드를 사용하는 문자나 이진 파일들은 MIME 포맷으로 변환되고 Content-Type을 지정하여 SMTP로 전송됨
    - 실질적으로 SMTP로 전송되는 대부분의 전자 우편은 MIME 형식임
    - 내부적으로 Base64 등의 인코딩을 사용


## Javax.mail SMTP 구현 예제
### Prerequisite
- Naver 메일을 사용하였으므로 어플리케이션용 비밀번호를 발급
- Naver 메일 설정에서 SMTP 사용 가능하도록 설정 변경

### Gradle
실행할 Java 프로젝트에서 아래와 같이 javax.mail 의존성 추가 (Gradle 기준)
```groovy
dependencies {
    implementation "javax.mail:mail:{TARGET_VERSION}"
}
```

### Java Main
```java
public class Mail extends Authenticator {
  public static final String MY_MAIL = "[메일 주소]";
  public static final String PASS = "[인증에 필요한 비밀번호]";

  public static void main(String[] args) throws ServletException, IOException {
    Mail mail = new Mail();
    mail.service();
  }

	public void service() throws ServletException, IOException {
		String from_email = MY_MAIL;
		String to_email = MY_MAIL; 
		String mail_subtitle = "subtitle";
		String mail_content = "title";
		
		System.out.println("from_email = " + from_email);
		System.out.println("to_email = " + to_email);
		
		// 먼저 환경 정보를 설정해야 한다. 메일 서버 주소를 IP 또는 도메인 명으로 지정한다.
		Properties props = System.getProperties();
		props.setProperty("mail.transport.protocol", "smtp");
		props.setProperty("mail.host", "smtp.naver.com");
		props.put("mail.smtp.auth", "true");
		props.put("mail.smtp.port", "465");
		props.put("mail.smtp.socketFactory.port", "465");
		props.put("mail.smtp.socketFactory.class", "javax.net.ssl.SSLSocketFactory");
		props.put("mail.smtp.socketFactory.fallback", "false");
    props.put("mail.smtp.ssl.protocols","TLSv1.2");
    props.setProperty("mail.smtp.quitwait", "false");
		
		Authenticator auth = new MyAuthenticator();

		// 위 환경 정보로 "메일 세션"을 만들고 빈 메시지를 만든다
		Session session = Session.getDefaultInstance(props, auth);
		session.setDebug(true);
		
		MimeMessage msg = new MimeMessage(session);
		
		try {
			// 발신자, 수신자, 참조자, 제목, 본문 내용 등을 설정한다
			msg.setFrom(new InternetAddress(from_email, MY_MAIL));
			msg.addRecipient(Message.RecipientType.TO, new InternetAddress(to_email, MY_MAIL));
			msg.setSubject(mail_subtitle);
			msg.setContent(mail_content, "text/html; charset=utf-8");

			// 메일을 발신한다
      System.out.println(msg.toString());
			Transport.send(msg);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	static class MyAuthenticator extends Authenticator {
		public PasswordAuthentication getPasswordAuthentication() {
			String username = MY_MAIL;
			String password = PASS;
			return new PasswordAuthentication(username, password);
		}
	}
```

### 실행 결과
```
from_email = [메일 주소]
to_email = [메일 주소]
DEBUG: setDebug: JavaMail version 1.4.7
javax.mail.internet.MimeMessage@42f30e0a
DEBUG: getProvider() returning javax.mail.Provider[TRANSPORT,smtp,com.sun.mail.smtp.SMTPTransport,Oracle]
DEBUG SMTP: useEhlo true, useAuth true
DEBUG SMTP: trying to connect to host "smtp.naver.com", port 465, isSSL false
DEBUG SMTP: connected to host "smtp.naver.com", port: 465

EHLO localhost
250-smtp.naver.com Pleased to meet you
250-SIZE 20971520
250-8BITMIME
250-PIPELINING
250-SMTPUTF8
250-AUTH PLAIN LOGIN
250 ENHANCEDSTATUSCODES
DEBUG SMTP: Found extension "SIZE", arg "20971520"
...
DEBUG SMTP: Attempt to authenticate using mechanisms: LOGIN PLAIN DIGEST-MD5 NTLM 
DEBUG SMTP: AUTH LOGIN command trace suppressed
DEBUG SMTP: AUTH LOGIN succeeded
DEBUG SMTP: use8bit false
MAIL FROM:<메일 주소>
RCPT TO:<메일 주소>
DEBUG SMTP: Verified Addresses
DEBUG SMTP:   "메일 주소" <메일 주소>
DATA
...
Subject: subtitle
MIME-Version: 1.0
Content-Type: text/html; charset=utf-8
Content-Transfer-Encoding: 7bit

title
.
QUIT
```


***References***
- [https://www.cloudflare.com/ko-kr/learning/email-security/what-is-smtp/](https://www.cloudflare.com/ko-kr/learning/email-security/what-is-smtp/)
- [https://datatracker.ietf.org/doc/html/rfc5321#section-4.1](https://datatracker.ietf.org/doc/html/rfc5321#section-4.1)
- [https://ko.wikipedia.org/wiki/MIME](https://ko.wikipedia.org/wiki/MIME)