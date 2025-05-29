### ASCII란
American Standard Code for Information Interchange의 약어
8비트 중 1비트를 오류검출, 7비트를 알파벳, 숫자, 특수문자 등을 표현하는데에 사용
가장 기초적인 문자 인코딩 방식

각 문자들은 아래와 같은 방식으로 매핑됨
![[Pasted image 20250529142554.png|600]]

### 코드 예제
```Java
public static void main(String[] args) {
    System.out.println(HexFormat.of().formatHex("Hello".getBytes()));
}
```

위 코드를 실행하면 결과값으로 "48656c6c6f"를 얻을 수 있음
ASCII 테이블에서 확인해보면 실제로 H, e, l, o에 대응하는 Hex는 각각 48, 65, 6c, 6f 임을 확인 가능