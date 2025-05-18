### Gradle이란
Groovy 기반의 빌드 자동화 도구. XML 기반인 Maven과 더불이 많이 쓰임.

### Gradle Project Structure
```bash
.
├── build.gradle
├── .gradle
├── gradle
│     └── wrapper
│           ├── gradle-wrapper.jar
│           └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
```
- build.gradle - 프로젝트에 필요한 의존성 및 빌드 내용 정의
- .gradle - 작업 파일
- gradle/* - gradle wrapper 관련 디렉토리
- gradlew, gradlew.bat - 각각 리눅스/윈도우 환경에서의 실행 명령
- setting.gradle - 프로젝트 설정 정보 작성 (프로젝트명 등)

### Gradle Wrapper
gradlew를 사용하면 gradle이 설치되지 않아도 빌드 가능
사용자가 프로젝트를 만든 사람과 동일한 버전으로 빌드 가능

### 빌드 방법
- 프로젝트 디렉토리에서 gradle init으로 structure 생성
- gradle
    - gradle build - 프로젝트 컴파일
    - gradle run - 프로젝트 컴파일 후 실행
    - gradle jar - 프로그램을 jar로 패키징
    - gradle clean - build 폴더 제거 (이전 상태로 되돌리는 것)
- gradlew
    - gradlew build - 프로젝트 컴파일
        - -x test 등으로 test 배제하는 등의 옵션 존재

### build.gradle
```groovy
buildscript {
    ext {
        springBootVersion = '2.3.7.RELEASE'
        lombokVersion = '1.18.10'
    }
    repositories {
        mavenCentral()
        jcenter()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group 'gradle.test.javaapp'
version '1.0-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
    mavenCentral()
    jcenter()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    // api '...'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'

    compileOnly "org.projectlombok:lombok:$lombokVersion"
    // runtimeOnly '...'

    annotationProcessor "org.projectlombok:lombok:$lombokVersion"
}
```
- buildscript
    - gradle로 task 실행 시 사용되는 설정
    - 즉 어플리케이션 빌드와는 별개의 설정이다.(위 처럼 `repositories`, `dependencies`를 따로 구현해야함.)
- ext
    - 전역변수 블록
    - 전역변수는 `$전역변수명`으로 사용 가능
- classpath
    - 라이브러리를 클래스 경로에 추가
    - 빌드에서 실행까지 의존하는 라이브러리를 지정
- plugin
    - 프로젝트에서 사용하는 Gradle 플러그인을 추가한다.(위에 설정된 플러그인들은 부트 환경구성에 필요한 플러그인)
    - eclipse : eclipse IDE 에서도 해당 Gradle project를 개발할 수 있도록 플러그인이 설치됨.
- group / version / sourceCompatibility
    - 프로젝트 생성 시 groupId, 어플리케이션 버젼, 자바버젼
- repositories
    - 필요한 라이브러리를 다운로드할 저장소를 지정
    - 공개저장소(jcenter)와, maven저장소를 사용할 수 있다.
    - 상호보완 되도록 둘 다 사용하는 것을 권장한다.
- dependencies (compile, api, implementation)
    - 라이브러리 추가
    - compile, api
        - 모듈 수정 시, 해당 모듈을 의존하고있는 모듈을 모두 빌드 -> 느리다.
        - `compile`의 경우 Gradle 3.0부터는 사용안하는 것을 권장(`api`로 대체)`A(api) <- B <- C 로 의존하는 구조라면, A 수정 시 B,C 모두 빌드`
    - implementation
        - 모듈 수정 시, 해당 모듈을 직접 의존하는 모듈만 빌드 -> 비교적 빠르다.`A(implementation) <- B <- C 로 의존하는 구조라면, A 수정 시 B 만 빌드`
    - testImplementation
        - 테스트에 사용하는 라이브러리
    - annotationProcessor
        - 어노테이션 기반 라이브러리를 컴파일러가 인식하도록 함 (lombok 등)
    - compileOnly
        - compile에만 필요하고, runtime에는 필요없는 라이브러리
            - 잘못 사용하면 런타임에 클래스를 못찾으니 주의!

**References**
- https://velog.io/@franc/Gradle-%EA%B8%B0%EB%B3%B8%EC%82%AC%EC%9A%A9%EB%B2%95
- https://velog.io/@skyepodium/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-GroupId-ArtifactId-Version