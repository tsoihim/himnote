<aside> 

본 페이지는 본인이 귀찮았던 나머지 ChatGPT에 기반하여 작성됨 그에게 외주를 넘기고 검수 및 정리하여 내용을 다듬었음

</aside>

## JPA란
---
JPA(Java Persistence API)는 자바에서 관계형 데이터베이스를 객체 지향적으로 다룰 수 있도록 해주는 표준 ORM(Object-Relational Mapping) 기술
Spring Boot에서는 Hibernate를 기본 JPA 구현체로 사용

### 주요 특징
- 객체와 관계형 데이터베이스의 매핑 지원
- 자동으로 SQL 쿼리 생성 및 실행
- 트랜잭션 관리 및 캐싱 지원
- JPQL(Java Persistence Query Language) 사용 가능

## 코드 예제
---
### Gradle 설정
```groovy
dependencies {
		...
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    runtimeOnly 'org.postgresql:postgresql' // H2, MySQL 등으로 변경 가능
    ...
}
```
### Entity 클래스
```java
@AllArgsConstructor(access = AccessLevel.PROTECTED)
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Entity
@Table(name = "USER")
@Getter
@ToString
public class User {
    @Id
    @Column(nullable = false, unique = true)
    private Long id;

    private String name;
    
    // @Convert(converter = [SOME_CLASS].class)로 변환/역변환도 가능
    private String email;

    @CreatedDate
    @Column(nullable = false)
    private LocalDateTime createdAt;
    
    
    // 이외에 Update 등을 위해 추가적인 method를 선언해두고 사용하자
}
```
### Repository 클래스
```java
public interface UserRepository extends JpaRepository<User, Long> {
	Optional<User> findByName(String name);
}

```
### Service 클래스에서 호출
```java
@Service
public class UserService {
    private final UserRepository userRepository;

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    @Transactional(readOnly = true)
    public User getUserById(Long id) {
        return userRepository.findById(id).orElse(null);
    }

    @Transactional
    public User saveUser(User user) {
        return userRepository.save(user);
    }
    
    @Transactional
    public void renameUser(String newName) {
        // 상응하는 로직을 만들고 dirty-checking 이용
        // retrieve -> update entity
    }
    
    @Transactional
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}

```

## 주의사항
---
### DDL-AUTO
- `spring.jpa.hibernate.ddl-auto=[create, update, validate, etc.]`
    - 상황에 맞는 ddl-auto 값을 써야 재앙 방지 가능
    - production level에서는 ddl이나 dml이 호출되는 설정을 최대한 지양해야함
### @Transactional
위 어노테이션을 빼먹으면 아래와 같은 문제 발생 가능
- **데이터 일관성 문제**
    - JPA는 기본적으로 **영속성 컨텍스트(Persistence Context)** 를 사용하여 엔티티를 관리하는데, `@Transactional`이 없으면 같은 트랜잭션 내에서 일관된 데이터 상태를 유지하기 어려움
    - 예를 들어, `saveUser()` 메서드에서 `userRepository.save(user);` 호출 후, 같은 트랜잭션 내에서 바로 조회하면 저장되지 않은 것처럼 보일 수 있음
- **지연 로딩(Lazy Loading)**
    - `@Transactional(readOnly = true)` 없이 `getUserById(Long id)` 같은 메서드를 호출하면, 연관된 엔티티를 지연 로딩할 때 `LazyInitializationException`이 발생할 가능성이 있음
    - 예를 들어, `User` 엔티티가 다른 엔티티와 `@OneToMany` 같은 관계를 갖고 있을 때, 서비스 메서드가 종료되면서 세션이 닫혀버려서 이후에 연관 데이터를 조회할 수 없게 됨
- **트랜잭션 롤백이 안됨**
    - `@Transactional`을 사용하면 예외 발생 시 자동으로 롤백되지만, 없다면 예외가 발생해도 변경된 데이터가 DB에 그대로 남을 수 있음
    - 예를 들어, `saveUser()`에서 `userRepository.save(user);` 후, 예외가 발생하면 이미 저장된 데이터가 롤백되지 않아 DB에 불완전한 데이터가 남을 수 있음
- **쓰기 작업에서 성능 저하**
    - `@Transactional`이 없으면 각 `save()`, `delete()` 호출마다 개별 트랜잭션이 생성됨. 여러 개의 작업이 있을 경우 성능 저하와 함께 불필요한 데이터베이스 부하가 발생할 수 있음
    - `@Transactional`을 적용하면 하나의 트랜잭션 내에서 여러 작업을 처리할 수 있어 **성능 최적화** 가능

## 영속성 컨텍스트의 동작
---
Entity가 변경될 때 영속성 컨텍스트는 아래와 같이 동작함
Entity Update 시 DB까지 데이터가 변경되는 이유는 **영속성 컨텍스트**와 **더티 체킹** 덕분
### **영속성 컨텍스트**
- JPA에서 `EntityManager`가 관리하는 엔티티 객체들의 집합
- `find()` 같은 메서드로 가져온 엔티티는 **영속성 컨텍스트에 의해 관리됨**
- 트랜잭션이 유지되는 동안, 영속성 컨텍스트는 엔티티의 상태 변화를 감지할 수 있음
### **더티 체킹**
- JPA는 트랜잭션이 끝나기 전에 **변경된 엔티티를 자동으로 감지하고 업데이트 쿼리를 생성**
- 엔티티의 필드 값을 직접 변경하면 `save()` 같은 메서드를 호출하지 않아도 DB에 반영됨
### **예제 코드**
```java
@Transactional
public void updateUser(Long id, String newName) {
    User user = userRepository.findById(id).orElseThrow();
    user.setName(newName); // 값 변경
    // `save()` 호출 안 해도 JPA가 자동으로 UPDATE 수행
}
```

- 위 코드를 실행하면, `setName(newName)`을 호출했을 뿐인데 트랜잭션이 끝날 때 `UPDATE user SET name=? WHERE id=?` 쿼리가 실행됨
### **더티 체킹이 동작하는 과정**
1. `findById(id)`를 호출하면 영속성 컨텍스트에 **영속 상태**의 엔티티가 저장됨
2. `user.setName(newName);`으로 필드 값을 변경
3. 트랜잭션이 끝나기 전에 JPA는 **변경 감지(Dirty Checking)** 를 수행
4. 변경된 부분이 있으면 자동으로 `UPDATE` SQL 실행
### **더티 체킹이 동작하지 않는 경우**
- 엔티티가 **영속 상태(Persistent State)** 가 아닐 때
    ```java
    {
    	...
    	User user = new User();
    	user.setId(1L);
    	user.setName("New Name");
    	// 이 상태에서는 DB에 반영되지 않음 (비영속 상태)
    }
    ```
    
    - `save(user)`를 호출해야 반영됨.
물론 flush 등을 호출해 바로 커밋을 적용하고 조회를 다시 하는 등의 작업도 할 수 있음