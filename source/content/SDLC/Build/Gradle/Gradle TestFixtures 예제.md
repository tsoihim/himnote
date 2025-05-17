## 개요
Gradle에서 멀티프로젝트를 구성하는 경우 테스트 코드에서 여러 프로젝트가 내부 프로젝트의 동일한 코드를 testImplementation 등으로 공유하고 싶은 경우가 있다

이런 경우를 위하여 Gradle에서는 testFixtures 기능을 제공함

## 예제
### 가정
- A 프로젝트에 있는 특정 테스트용 코드를 B 프로젝트 테스트에서 사용하고 싶은 경우
### A 프로젝트 설정
1. 빌드 스크립트 작성
```groovy
plugins {
	id 'java'
	id 'java-library'
	id 'java-test-fixtures'
}

apply plugin: 'java-test-fixtures' // plugin 설정

dependencies {
	testFixturesApi '...' // testFixtures에서 의존성이 있다면 좌측과 같이 설정
}
```
2. 코드 작성
testFixtures 디렉토리를 src 하위에 만들어 공유 클래스들을 작성
```bash
nara@nara-MacBook:src ls
main         test         testFixtures
```
해당 공유 클래스는 아래와 같이 JUnit Tag를 래핑한 어노테이션을 제공
```java
public class TestAnnotations {
    @Target({ ElementType.TYPE, ElementType.METHOD })
    @Retention(RetentionPolicy.RUNTIME)
    @Tag("test")
    public @interface TestTag {
    }
		...
}
```
### B 프로젝트 설정
1. 빌드 스크립트 작성
```java
testImplementation(testFixtures(project(':A'))) // testImpl 설정
```
2. 코드 작성
```java
import ...TestAnnotations.TestTag;

@TestTag
public class NaraTest {
		...
}
```


**References**
- [https://docs.gradle.org/current/userguide/java_testing.html#sec:java_test_fixtures](https://docs.gradle.org/current/userguide/java_testing.html#sec:java_test_fixtures)