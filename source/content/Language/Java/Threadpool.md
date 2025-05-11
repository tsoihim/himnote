### ThreadPool이란
필요할 때마다 스레드를 생성/삭제할 필요없이, 미리 스레드들을 생성해놓은 후 사용하기 위한 스레드의 풀
- 미리 생성해둔 자원!

사전 정의한 워커나 스레드들을 이용하는 경우라면 스레드풀을 통해 스레드만 생성 후 큐를 (ArrayBlockingQueue, LinkedblockingQueue 등) 이용해서 직접 스케줄링을 할 수도 있음

간단한 러너블을 스레드별 스케줄링 시킬거라면 executorService를 사용하는 것이 편함.
<aside> 💡 @FunctionalInterface public interface Runnable

스레드에 의해 인스턴스가 실행될 클래스라면 Runnable을 꼭 구현해야함 (no arg인 run 메소드)

</aside>

아래와 같이 static factory 메소드를 이용하여 스레드풀 생성/일감 할당 등 보편적인 기능 사용할 수 있음
```java
private ExecutorService executorService;
    
public HostMonitor(final int threadPoolSize) {
	this.executorService = Executors.newFixedThreadPool(threadPoolSize);
	...
}

private class Task implements Runnable {
	@Override
	public void run() {
		...
	}
}

public static void main(String[] args) {
	executorService.execute(new Task());
	executorSercice.close(); // 작업이 끝난 후엔 자원회수가 필수적임
}
```