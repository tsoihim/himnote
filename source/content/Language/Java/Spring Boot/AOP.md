> AOP complements OOP by providing another way of thinking about program structure. The key unit of modularity in OOP is the class, whereas in AOP the unit of modularity is the aspect. Aspects enable the modularization of concerns (such as transaction management) that cut across multiple types and objects. (Such concerns are often termed "crosscutting" concerns in AOP literature.)
> 
> One of the key components of Spring is the AOP framework. While the Spring IoC container does not depend on AOP (meaning you do not need to use AOP if you don’t want to), AOP complements Spring IoC to provide a very capable middleware solution.

## 개념
Spring AOP는 pure Java로 구현됨. 특별한 컴파일 과정이 필요하지 않으며 class loader 구조에 대한 제어도 필요치 않도록 설계되어 있음. 현재는 join point로 method execution만 지원
Spring AOP의 구현 목표 및 본질은 엔터프라이즈 어플리케이션의 요구사항을 만족하기 위해 AOP와 Spring IoC를 통합하는데 있음. (단순히 full-feature AOP 프레임워크를 만드는 것이 목표가 아님)
아래와 같은 term들과 정의가 사용됨
### Aspect
여러 클래스에 퍼져서 분포하는 관점(관심)을 모듈화한 것(by cut). Spring AOP는 두 종류의 방식; schema-based approach 와 @AspectJ annotation style 지원 ([[AspectJ]])
### Join point
메소드 호출 등 프로그램의 특정 실행 지점. Spring AOP에서는 join point를 항상 메소드 실행으로 정의
### Advice
특정 join point에서 어떤 관점에 의해 수행되는 행동. around/before/after와 같은 종류의 advice가 있음. Spring은 join point를 둘러싼 인터셉터와 체이닝을 통해 advice 지원

Spring에서 지원하는 advice 종류는 아래와 같음
- Before advice
    - join point 전에 실행되나 exception을 throw하지 않는 이상 join point 실행을 막을 수는 없음
- After returning advice
    - join point 정상 실행 이후 동작하는 advice
- After throwing advice
    - join point가 exception을 발생시킨 이후 동작하는 advice
- After (finally) advice
    - join point의 정상 실행여부와 상관없이 무조건 후행하는 advice
- Around advice
    - join point 전과 후를 감싸는 advice. join point의 실행 여부도 결정하는 등 강력한 기능 지원
Spring은 강력한 AOP Advice들을 지원하나, 요구되는 기능에 맞는 최소한의 권한을 가진 advice를 사용할 것을 권장. 또한, 모든 advice의 파라미터들은 정적 타입으로 명확히 지정해야함
### Pointcut
join point 매치를 위한 predicate. Advice는 pointcut을 통해 매칭되는 joinpoint에 적용될 수 있음(ex. 특정 이름을 가지는 메소드 등). pointcut과 join point를 매칭시키는 개념은 AOP에서 핵심적인 것이며 Spring의 경우 AspectJ pointcut 표현식을 기본으로 사용
- 객체 지향 구조와 상관없이 pointcut은 advice가 join point와 매칭될 수 있도록 함
### Introduction
Advice 되는 타입에 부가적인 메소드나 필드를 선언하는 것. 이를 이용해 Advice 되는 객체에 새로운 인터페이스를 선언하거나 구현을 할 수 있다.
### Target object
관점에 의해 advice되는 객체이며 advised object라고도 불림. Spring AOP는 프록시 패턴으로 구현되므로, 당연히 해당 객체도 프록시화 되는 객체임
### AOP proxy
AOP 구현을 위해 프레임워크가 생성하는 프록시 객체 (JDK dynamic proxy 또는 CGLIB proxy)
### Weaving
Advised object를 생성하기 위해 여러 객체 및 타입과 관점들을 연결하는 것. Spring AOP의 경우 해당 작업은 runtime에 수행됨


**References**
- https://docs.spring.io/spring-framework/reference/core/aop.html