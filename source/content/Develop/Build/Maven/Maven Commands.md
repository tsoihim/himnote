Maven 패키지 설치
- mvn dependency:get -Dartifact=\[ARTIFACT]:\[VERSION]
    - ex) mvn dependency:get -Dartifact=org.springframework:spring-instrument:3.2.3.RELEASE

Maven 의존성 목록 확인
- mvn dependency:list

Maven 의존성 트리 확인
- mvn dependency:tree