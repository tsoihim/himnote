### POJO란
Plain Old Java Object란 Java언어 규약에서 정의된 것 이외의 기술에 제약이 없는 Java Object를 칭함

POJO에서는 아래와 같은 것을 지양함
1. 미리 지정된 클래스를 extends    
    ```
    public class Foo extends javax.servlet.http.HttpServlet { ... }
    ```
2. 미리 정의된 인터페이스를 implement
    ```
    public class Bar implements javax.ejb.EntityBean { ... }
    ```
3. 미리 정의된 Annotation을 포함
    ```
    @javax.persistence.Entity
    public class Baz { ... }
    ```

\*Hibernate를 사용할 때 어떻게 했었는지 생각해보자!

### 왜 하는가?
객체지향의 특성을 살려 비즈니스 로직에 충실할 수 있도록 함
- 특정 환경이나 기술에 종속적이지 않으면 재사용이 가능하고, 확장 가능한 유연한 코드를 작성 가능
- 저수준 레벨의 기술과 환경에 종속적인 코드를 제거하여 코드를 간결해지며 디버깅하기에도 상대적으로 용이
- 특정 기술이나 환경에 종속적이지 않기 때문에 테스트가 단순화됨
    - POJO의 꽃은 테스트
- 객체지향적인 설계를 제한 없이 적용 가능

### 풍성한 도메인 모델
POJO의 기본 특징은 하나의 오브젝트 안에 상태(State)와 행위(Behavior)를 모두 가지고 있는 것
- 즉, 인스턴스 변수와 로직을 가진 메소드를 가지고 있는 것
객체지향 원리에 충실하게 도메인 모델을 만드는 것을 풍성한 도메인 모델(Rich Domain Model)이라 함

**References**
- [https://velog.io/@dion/what-is-POJO](https://velog.io/@dion/what-is-POJO)