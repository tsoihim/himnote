### Primitive Data Types
Java는 변수가 사용되기 전 이름과 타입을 명시해야하는 정적 타입 언어
- `int count = 1;`
원시 데이터 타입은 Java 언어에서 예약어로 설정되어있으며, 객체가 아님
총 8개의 데이터 타입이 있으며, 초기화하지 않아도 각 타입별 기본값이 자동 할당됨

### 타입 종류
- byte
	- 8-bit signed two's complement integer
	- default : 0
- short
	- 16-bit signed two's complement integer
	- default : 0
- int
	- 32-bit signed two's complement integer
	- default : 0
- long
	-  64-bit two's complement **integer**
	- default : 0L
- float
	- single-precision 32-bit IEEE 754 floating point
	- default : 0.0f
- double
	- double-precision 64-bit IEEE 754 floating point
	- default : 0.0d
- boolean
	- true/false
	- default : false
- char
	-  single 16-bit Unicode character
		- 1바이트가 아님에 유의. 따라서 char로 바이트 처리하면 위험
	- default : '\u0000'


**References**
- https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html