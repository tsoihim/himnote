## **Annotation이란**
어노테이션은 그 자체로 프로그램의 일부는 아니나, 프로그램 자체에 메타데이터의 한 형식
어노테이션 자체로는 annotate한 코드의 동작 그 자체에 직접적인 영향을 줄 수는 없음

아래와 같은 주된 기능을 가짐
- 컴파일러에게 정보 제공
    - 에러 탐지 혹은 warning suppression 등
- 컴파일 타임 혹은 deployment 타임 프로세싱
    - 어노테이션에 정의한 바와 같이 코드를 생성하는 등
- 런타임 프로세싱
    - 일부 어노테이션은 런타임에 참조됨

## Built-in Annotations
아래는 자체 제공되는 Annotation들의 일부
- Deprecated
- Override
- SuppressWarnings
- Retention
    - `RetentionPolicy.SOURCE` : 소스 레벨까지만 retain, 컴파일러는 무시
        - lombok @Getter 등
    - `RetentionPolicy.CLASS` – 컴파일 타임까지만 retain, JVM은 무시
        - lombok @NonNull 등
    - `RetentionPolicy.RUNTIME` – JVM 런타임까지 계속 retain
        - spring boot @Component 등

**References**
- [https://docs.oracle.com/javase/tutorial/java/annotations/index.html](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)