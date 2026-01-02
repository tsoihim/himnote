### 개요
AspectJ는 Java 언어를 위한 [[AOP]] 도구로, 바이트코드 weaving을 통해 이를 수행하도록 구현되어있음
주요 아티팩트로 aspectjtools, aspectjrt(Runtime)를 제공
바이트코드 weaving을 이용하므로, 소스코드가 없는 타겟 (동적 링크된 jar의 클래스)에도 적용 가능

### Weaving
책임과 역할에 따라 분리된 타겟 객체에서 공통 관심사를 잘라내어 (PointCut) 원하는 aspect를 이식 
- Proxy를 통해 구현하는 것이 아니며, 바이트코드 직접 수정
- apect와 클래스를 결합하여 JVM에서 실행되는 class 파일을 재생성

아래와 같이 세가지 방식의 weaving이 있음
- CTW (Compile-Time Weaving) : 소스 컴파일 시점에 `ajc` 컴파일러가 Aspect 코드 직접 삽입
- PCW (Post-Compile Weaving) : 이미 빌드된 JAR 파일의 바이트코드를 분해하여 재조립
- LTW (Load-Time Weaving) : JVM의 클래스 로딩 시점에 `javaagent`로 실시간 코드 이식

### 코드 예제
아래와 같이 aspectj 의존성 추가 및 코드 작성 후 테스트

<pom.xml>
```xml
...
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.9.22</version> </dependency>
        <dependency>
            <groupId>org.eclipse.angus</groupId>
            <artifactId>angus-mail</artifactId>
            <version>2.0.3</version>
        </dependency>
...
    <build>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>aspectj-maven-plugin</artifactId>
                <version>1.14.0</version>
                <configuration>
                    <complianceLevel>17</complianceLevel> <source>17</source>
                    <target>17</target>
                    <showWeaveInfo>true</showWeaveInfo>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
...         
```

<Aspect 코드>
```java
@Aspect
public class testAspect {
	@Around("execution(*org.apache.something.target.method(..))")
	public Object test(ProceedingJoinPoint joinPoint) {
		...
	}
}
```

<pom.xml>
```xml
<!DOCTYPE aspectj PUBLIC "-//AspectJ//DTD//EN" "https://www.eclipse.org/aspectj/dtd/aspectj.dtd">
<aspects>
    <aspect name="[Aspect 코드 경로]"/>
    <weaver options="-showWeaveInfo -verbose">
        <include within="[타겟 클래스 경로]"/>
    </weaver>
</aspects>         
```

### 실행 방법
Java 명령 실행 시 아래와 같이 인자 주어야함 (LTW)
- `java -javaagent:path/to/aspectjweaver-1.9.22.jar -jar your-app.jar`


**References**
- [The AspectJTM Development Environment Guide](https://eclipse.dev/aspectj/doc/latest/devguide/)