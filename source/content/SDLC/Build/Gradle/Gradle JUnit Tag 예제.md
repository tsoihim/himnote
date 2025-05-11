## 개요
Tag 어노테이션을 사용하는 [[JUnit]] 기반 테스트 코드가 있는 프로젝트에서 Tag를 활용하는 방법 서술

## 예제
아래와 같은 SNMP 관련 테스트 코드가 있음
```java
@Test
    @Tag("first")
    public void Snmp_테스트() {
			...
    }

    @Test
    @Tag("second")
    public void SnmpCommunity_테스트() {
			...
    }
```

Gradle “test” task에서 Snmp_테스트만 수행되도록 하고 싶은 경우, build.gradle을 아래와 같이 구성
```groovy
tasks.named('test') {
    useJUnitPlatform {
        includeTags 'first'
    }
}
```

gradle test를 수행하면 build 하위에 아래와 같이 한 테스트 case만 수행된 것 확인 가능
```xml
<?xml version="1.0" encoding="UTF-8"?>
<testsuite ... hostname="nara-MacBook.local" time="23.034">
  <properties/>
  <testcase name="Snmp_테스트()" ...>
	<system-out>
...
```

이번에는 test외에 다른 task를 추가하고 second 태그만 실행시키고 싶은 경우의 빌드 스크립트
```groovy
 task secondTest(type: Test) {
    useJUnitPlatform {
        includeTags 'second'
    }
}
```

gradle secondTest를 수행하면 build 하위에 secondTest 디렉토리를 거쳐 위와 같은 XML이 생성됨
```bash
nara@nara-MacBook:test-results tree -L 1
.
├── secondTest
└── test
```
- JUnit includeTags의 경우 아래와 같이 and/or 등의 combination으로 지정 가능
    - `includeTags 'first & second'`
- Untagged의 경우 ‘none()’으로 표기