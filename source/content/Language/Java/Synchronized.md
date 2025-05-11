### Synchronized란
현재 데이터를 사용하고 있는 스레드 외에는 해당 데이터에 대한 접근을 제한하는 Java 키워드
- 임계 영역 설정
크게 네가지의 방식으로 사용 가능
- synchronized method
- synchronized block
- static synchronized method
- static synchronized block

### 예제 (w/Threadpool)
```java
    public static void main(String[] args) {

        class Region {
            static Integer value = 0;

            synchronized static String region() {
                return value.toString() + (++value).toString();
            }
            // block을 사용했다면 아래와 같이 표현
            // static String region() {
                // synchronized (Region.class) {
                    // return value.toString() + (++value).toString();
                // }
            // }
        }
        
        class Gnx implements Runnable {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + " : " + Region.region());
            }
        }

        ExecutorService executorService = Executors.newFixedThreadPool(3);
        
        for (int i=0 ; i<10 ; i++) {
            executorService.submit(new Gnx());
        }
        executorService.close();

        System.out.print(Region.region());
    }
}
```
- 실행 결과
    - synchronized 선언을 하지 않았다면 마지막 값이 1011로 출력되지 않을 수 있음
```
pool-1-thread-2 : 12
pool-1-thread-3 : 23
pool-1-thread-1 : 01
pool-1-thread-1 : 34
pool-1-thread-3 : 56
pool-1-thread-2 : 45
pool-1-thread-1 : 67
pool-1-thread-3 : 78
pool-1-thread-2 : 89
pool-1-thread-1 : 910
1011%
```