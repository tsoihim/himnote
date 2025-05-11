## Lombok이란
getter/setter, equals 등의 메소드를 annotation 통해 손쉽게 생성할 수 있도록 도와주는 등 개발자 편의성을 지원하는 자바 라이브러리

## Stable Features
### [`@NonNull`](https://projectlombok.org/features/NonNull)
메소드나 생성자의 파라미터에 적용
- 자동으로 null-check문이 생성되어 exception이 발생하도록 만들 수 있음
field에 적용
- setter에서 null-check 문이 생성됨
- RequiredArgsConstructor, AllArgsConstructor에서 null-check 문이 생성됨

- 즉, lombok에 생성하여 주는 해당 필드 세팅 관련 메소드에서 null-check문 생성됨
### [`@Getter/@Setter`](https://projectlombok.org/features/GetterSetter)
field나 class에 적용
- class에 적용하면 static이 아닌 모든 필드에 getter/setter 생성
default getter/setter를 자동으로 만들기 위해 사용
- default getter는 field 반환 메소드를 자동으로 생성
- default setter는 field set하는 메소드를 자동으로 생성

아래와 같이 AccessLevel을 정하지 않으면 생성되는 메소드는 자동으로 public이 됨
- 아래와 같이 지정하면 미리 생성된 메소드가 오버라이드됨 (선순위)
```java
@Getter(AccessLevel.PRIVATE)
@Setter(AccessLevel.PRIVATE)
```

### [`@ToString`](https://projectlombok.org/features/ToString)
Class에 적용하여 class의 toString 메소드를 자동으로 구현하여줌
아래와 같이 exclude 정책 적용 가능
```java
// 필드에 적용하여 toString에서 배제
@ToString.Exclude 

// Include 적용한 필드만 생성되는 문자열에 포함
@ToString(onlyExplicitlyIncluded = true) 
@ToString.Include
```

### [`@EqualsAndHashCode`](https://projectlombok.org/features/EqualsAndHashCode)
<aside> 💡 Object.equals는 기본적으로 객체가 동일한지 판단하나 객체가 동등한지 판단하기 위해 오버라이드

Object.hashCode는 객체의 메모리 주소를 반환 (native code로 구현됨)

동일한 객체라면 동일한 메모리 주소를 가질 것이므로 동일 객체는 동일 해시코드를 가져야함

따라서 equals를 오버라이드하면 hashCode도 오버라이드해야함

(HashTable, HashMap 등에서 자료 저장 위치 결정을 위해 hashCode를 이용)

*다른 객체임에도 같은 hashCode를 사용하는 것에 대한 불이익 감수 필요
</aside>
- String 클래스 equals 메소드
```java
public boolean equals(Object anObject) {
	if (this == anObject) {
		return true;
	}
	return (anObject instanceof String aString)
		&& (!COMPACT_STRINGS || this.coder == aString.coder)
		&& StringLatin1.equals(value, aString.value);
}
```

클래스에 적용, 클래스에 대한 hashCode 메소드와 equals 메소드를 구현하여줌
- default로 모든 non-static 필드를 사용하나 아래와 같이 일부를 배제하거나 포함할 수 있음
```java
// 필드에 적용하여 equals, hashCode에서 배제
@EqualsAndHashCode.Exclude 

// Include 적용한 필드만 equals, hashCode에서 사용
@EqualsAndHashCode(onlyExplicitlyIncluded = true) 
@EqualsAndHashCode.Include
```
### [`@NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor`](https://projectlombok.org/features/constructor)
- AllArgsConstructor
    - 모든 필드를 매개변수로 입력받는 생성자 생성
- RequiredArgsConstructor
    - final, @NonNull이 붙은 필드만 매개변수로 입력받는 생성자 생성
- NoArgsConstructor
    - 매개변수가 없는 생성자 생성
    - @NoArgsConstructor(force=true)로 설정하면 강제로 모든 필드를 0 / false / null로 초기화
    - @NonNull 필드가 있다면 check가 수행되지 않음!
### [`@Data`](https://projectlombok.org/features/Data)
클래스에 적용
@ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor를 한번에 생성

제약을 두고 싶다면 각 어노테이션들을 재정의, 오버라이드하여 제약을 준 채로 사용하면 됨

### [`@Value`](https://projectlombok.org/features/Value)
클래스에 적용
@Data와 비슷하지만 모든 필드를 private final로 만들어줌. 당연히 setter는 생성되지 않음. 클래스 또한 final
- final @ToString @EqualsAndHashCode @AllArgsConstructor @FieldDefaults(makeFinal = true, level = AccessLevel.PRIVATE), @Getter와 동일
- 생성자는 따로 생성하거나 어노테이션으로 생성 필요
### [`@Builder`](https://projectlombok.org/features/Builder)
클래스에 적용, 아래와 같은 빌드 패턴으로 사용할 수 있도록 코드 생성하여줌
생성자 어노테이션이 없으면 AllArgsConstructor를 자동으로 생성
- 따라서 다른 생성자 어노테이션과 쓰려면 주의 필요 (접근 제어 등)
```java
Person.builder()
	.name("Adam Savage")
	.city("San Francisco")
	.job("Mythbusters")
	.job("Unchained Reaction")
	.build();
```

**References**
- [https://projectlombok.org/features/](https://projectlombok.org/features/)
- [https://www.nowwatersblog.com/springboot/springstudy/lombok](https://www.nowwatersblog.com/springboot/springstudy/lombok)