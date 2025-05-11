## Java.time
Java에서 기본으로 제공하는 시간 관련 라이브러리 집합

하위에 아래와 같은 클래스들이 있고, 해당 클래스들이 본 문서에서 다룰 내용
- java.time
    - java.time.LocalDateTime
    - java.time.ZonedDateTime
    - java.time.format
        - DateTimeFormatter

## DateTime
LocalDateTime과 ZonedDateTime으로 구분이 된다
- 둘의 차이는 time zone에 대한 고려 여부

두 클래스 모두 of, now, parse 등의 static method 통해 인스턴스화가 가능
`ZonedDateTime.parse(clock.getCurrentDatetimeString())`

### DateTimeFormatter
통상 시간을 String으로 변환하여 주고받는 일이 많으므로 아래와 같은 포맷 통해 문자열로 변환하는 경우가 잦다
- "yyyy-MM-dd'T'HH:mm:ss”

이럴 때 DateTime의 toString()과 같은 메소드에만 의존하게 되면 의도한 format과 달라지는 경우 발생 가능
- ex. 00초일 때 sec 표기가 누락되는 등

이 경우 DateTimeFormatter를 이용할 수 있음
DateTimeFormatter 또한 ofPattern과 같은 static method로 인스턴스화 가능
ZonedDateTime을 formatter를 이용해 포맷에 맞는 string으로 다시 생성하려면 아래와 같이 해볼 수 있음
(실제 용례와는 거리가 멀 수 있음)
```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd'T'HH:mm:ss");

ZonedDateTime.parse(clock.getCurrentDatetimeString()).format(formatter).toString());
```