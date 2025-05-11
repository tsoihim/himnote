### Annotations
- @BeforeAll
    - 해당 테스트에서 가장 처음 수행할 것들
        - 테스트에 필요한 환경 세팅 등
- @AfterAll
    - 해당 테스트에서 가장 마지막에 실행할 것들
        - 실제 장비를 연결해 테스트한다면 연결 종료 등
- @Disabled, @Ignore
    - 해당 테스트는 무시하고 실행시키지 않도록 함
    - 자동으로 실행될 테스트는 아니나 필요한 경우 이 방식으로 작성을 했음
        - 이런 경우엔 차라리 main 함수를 만들어서 해당 엔트리 포인트 실행하는 것은 어떨지?
- @Order
    - 단위 테스트를 넘어서 Order를 이용해 시나리오 테스트를 구축할 수 있을 것으로 보임
    - 특정 Order값을 지정하지 않았다면?
        - Default order value for elements not explicitly annotated with `@Order`, equal to the value of `Integer.MAX_VALUE / 2`
    - TestMethodOrder를 테스트 클래스에 선언하여 어떤 식으로 order를 지정할지 명시해야함
        - order annotation, asc, desc 등
- @TestInstance
    - 테스트 인스턴스의 생성 단위를 명시
        - PER_METHOD (by default)
        - PER_CLASS
- @Tag
    - 태깅을 통하여 테스트들을 분류, 빌드 시스템에서 태깅에 따른 동작을 정의할 수 있도록 해줌
    - 아래 문서 예제 참조
	    - [[Gradle JUnit Tag 예제]]

### 의문점
테스트 간의 실행 순서는?
- JUnit5 이상에서는 ClassOrder를 사용하여 정의할 수 있음