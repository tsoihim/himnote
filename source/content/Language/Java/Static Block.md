### **static block이란**
- 클래스가 로딩 후 클래스 변수가 준비되고나서 자동으로 실행되는 블록
- 한 클래스에 여러 static 블록을 둘 수 있음
- 클래스 변수를 초기화시키는 코드 등을 블록에 삽입

### **예제**
```java
class Example {
		...
		...
		static Retreiver retreiver;
    static Dispatcher dispatcher;
    static {
        try {
            logger = new SFLogger();
            logger.info("SFLogger start");
        } catch (IOException e) {
            e.printStackTrace();
        }

        slot = new Semaphore(20);
        new Thread(new Retreiver()).start();
        new Thread(new Dispatcher()).start();
    }
		...
		...
}
```

### 내부 동작
- 실행 순서
    1. 클래스 로딩
    2. 클래스 변수가 있으면 메모리를 생성
    3. static 블록이 선언된 순서대로 실행
        
- 참고 : 클래스 로딩 절차
    1. JRE 라이브러리 폴더에서 클래스 검색
    2. 없으면, CLASSPATH 환경 변수에 지정된 폴더에서 클래스 검색
    3. 찾았으면, 그 클래스 파일이 올바른 바이트코드인지 검증
    4. 올바른 바이트코드라면, Method Area 영역으로 파일을 로딩
    5. 클래스 블록이 있으면 순서대로 그 블록을 실행
    6. 클래스 안에 static block (스태틱 블록)들이 있으면 순서대로 그 블록을 실행

**References**
- [https://uoonleen.tistory.com/6](https://uoonleen.tistory.com/6)