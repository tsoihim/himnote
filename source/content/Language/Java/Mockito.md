### 개요
Mockito를 활용하면 mock 기반 테스트코드를 쉽게 작성할 수 있음

특히 SpringBootTest와 결합하면 테스트 대상 클래스가 수행하여야하는 작업을 여러 테스트 케이스별로 정의하고, 그에 따라 Mock을 생성하기 쉬움
- TDD향 개발에 유용!

아래 코드와 같이 사용 가능
```java
@SpringBootTest
public class SampleTest {
	@InjectMocks
	private TargetService targetService;

	@Mock
	private relatedService relatedService;

	@Test
	public void case1() {
		/*
		 * Test code with TargetService
		 * 상황에 맞게 Mockito.mock(), Mockito.when() 등 적용
		 */
	}
}
```

### 구성 요소
#### Annotation
- @Mock
	- 아래 메소드의 mock과 같은 역할을 함
- @InjectMocks
	- 필요한 Mock을 타겟 클래스에 주입하여줌
	- 위 코드를 예로 들면, 아래 작업을 수행해주는 것
		- targetService = new TargetService(relatedService);
#### Method
- mock(Class\<T> classToMock)
	- DTO 등 특정 클래스의 Mock 인스턴스가 필요할 때 사용
	- 접근 제어를 무시하고 사용 가능하게 해줌
- when(T methodCall)
	- 특정 메소드 콜이 일어났을 때 수행될 행동을 정의 가능
		- thenReturn, thenThrow 등
	- ex. when(relatedService.someMethod(anyString())).thenReturn(null);
- any(), anyInt(), anyString(), etc.
	- Argument Matcher로, 특정 인자를 위한 패턴 설정이 가능