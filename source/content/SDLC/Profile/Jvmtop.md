### Jvmtop이란
- Running java application에 attatch하여 사용할 수 있는 java 프로파일러
- https://github.com/patric-r/jvmtop

### 설치 방법
- jvmtop github 링크에서 release로 이동
- jvmtop tarball 다운로드 후 원하는 타겟 서버로 파일 이동 및 압축해제

### 사용방법
- ./jvmtop.sh
    - Overview 모드
- ./jvmtop.sh \[PID]
    - single vm 모드
- ./jvmtop.sh --profile \[PID]
    - single vm cpu profile 모드

### 출력 예시
```bash
JvmTop 0.8.0 alpha - 20:38:08,  amd64,  8 cpus, Linux 3.10.0-95, load avg 0.70
<http://code.google.com/p/jvmtop>

Profiling PID 23524:                     access-ui-1.8.13.jar

45.70% (     7.22s) ....mybatis.spring.SqlSessionTemplate$SqlSessionIntercep()
20.40% (     3.22s) ....springframework.orm.jpa.SharedEntityManagerCreator$D()
12.31% (     1.95s) ....springframework.transaction.support.TransactionSynch()
6.70% (     1.06s) ....mariadb.jdbc.internal.io.input.StandardPacketInputSt()
6.46% (     1.02s) ....springframework.boot.loader.LaunchedURLClassLoader.l()
4.63% (     0.73s) org.springframework.util.AntPathMatcher.tokenizePattern()
3.73% (     0.59s) ....springframework.core.annotation.AnnotationUtils.find()
0.07% (     0.01s) com.rabbitmq.client.impl.Frame.readFrom()
```