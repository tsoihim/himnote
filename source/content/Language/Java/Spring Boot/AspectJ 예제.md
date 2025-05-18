### 개요
Spring Boot 프로젝트에서 aop(spring boot starter의 aop) 의존성을 추가, AspectJ 이용하여 REST API의 GET Method가 호출될 때마다 로그를 적용

### 코드
- GetMapping 어노테이션을 사용하는 메소드에만 적용
- 메소드 호출 전과 호출 후 예외가 발생하지 않았을 때만 로그 출력
```java
package com.attoresearch.nouvelle.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TestAspect {
    @Pointcut("@annotation(org.springframework.web.bind.annotation.GetMapping)")
    private void cut() {
    }

    @Before(value = "cut()")
    public void beforeParameterLog(JoinPoint joinPoint) {
        System.out.println("*** AOP TEST : BEFORE ***");
    }

    @AfterReturning(value = "cut()", returning = "returnObj")
    public void afterReturnLog(JoinPoint joinPoint, Object returnObj) {
        System.out.println("*** AOP TEST : RETURN ***");
    }
}
```

### 실행 결과
- curl로 GetMapping 사용하는 아무 API나 호출
```
2024-09-15 13:37:22.052 https-jsse-nio-8643-exec-4 [INFO ] org.springframework.web.servlet.DispatcherServlet.initServletBean() 532 - Initializing Servlet 'dispatcherServlet'
2024-09-15 13:37:22.052 https-jsse-nio-8643-exec-4 [INFO ] org.springframework.web.servlet.DispatcherServlet.initServletBean() 554 - Completed initialization in 0 ms
*** AOP TEST : BEFORE ***
*** AOP TEST : RETURN ***
```


**References**
- https://docs.spring.io/spring-framework/reference/core/aop/ataspectj.html