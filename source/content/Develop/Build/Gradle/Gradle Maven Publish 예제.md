## 개요
Gradle 멀티 모듈프로젝트를 Maven Local Repo에 publish 이를 통해 로컬 내 다른 프로젝트에서 import하여 사용할 수 있도록 세팅하는 예제

## 예제
### Maven Local Repo 확인
```bash
nara@nara-MacBook:~ ls /Users/nara/.m2/repository/com
airlenet         esotericsoftware google           ibm              microsoft        querydsl         thoughtworks
alexecollins     ethlo            googlecode       jayway           mycila           rabbitmq         typesafe
..
```

### Gradle Build 스크립트 수정
```groovy
buildscript {
	ext {
		groupName = 'com.atomtesttest'
        projectVersion = '0.0.1'
	}
}

plugins {
	id 'java'
	id 'java-library'
	id 'maven-publish'
}

allprojects {
	group = groupName 
	version = projectVersion 
}

...

subprojects {
	apply plugin: 'java'
	apply plugin: 'java-library'
	apply plugin: 'maven-publish'
	
  ...
	
  publishing {
		publications {
			maven(MavenPublication) {
				groupId = groupName
				artifactId = project.name 
				version = projectVersion

				from components.java
			}
		}
	}
}
```

### Gradle publish 플러그인 실행
```bash
nara@nara-MacBook:anchor ./gradlew publishToMavenLocal

BUILD SUCCESSFUL in 750ms
29 actionable tasks: 18 executed, 11 up-to-date
```

### Maven Local Repo 확인
- atomtesttest가 생성된 것을 확인
```bash
nara@nara-MacBook:~ ls /Users/nara/.m2/repository/com
airlenet         esotericsoftware google           ibm              microsoft        querydsl         thoughtworks
alexecollins     ethlo            googlecode       jayway           mycila           rabbitmq         typesafe
atomtesttest
```

### Import
다른 프로젝트에서는 아래와 같이 local repo에서 의존성 가져오면 됨
```groovy
repositories {
    mavenLocal()
		...
}

dependencies {
    implementation '{groupId}:{artifactId}:{version}'
		...
}
```