> [!Note]
> HTTP/1.1 기준
## Common
Hypertext Transfer Protocol (HTTP)는 application layer 프로토콜로, 하이퍼미디어 정보를 주고받는 데에 사용됨
#### Rules
- Entity-body 이외에 모든 end-of-line 마커는 [[CRLF]]로 통일

## Header
메시지의 목적, 특성 등 메타데이터를 헤더에 담아 보낼 수 있음
general, request, response, entity 헤더로 분류
대표적인 헤더로 Date, Host, Server, Content-Type 등이 있음
#### 포맷
아래와 같이 "필드명:필드값"의 형태로 구성
```
message-header = field-name ":" [ field-value ]
field-name     = token
field-value    = *( field-content | LWS )
field-content  = <the OCTETs making up the field-value
                 and consisting of either *TEXT or combinations
                 of token, separators, and quoted-string>
```
#### 예제
간단한 Hello API를 만들어 Curl을 날려보면 아래와 같은 결과가 출력되는 것을 확인 가능
``` 
$ curl -ki https://127.0.0.1:8443/hello
HTTP/1.1 200 
Date: Fri, 30 May 2025 02:06:03 GMT
Content-Type: text/plain;charset=UTF-8
Content-Length: 7

Hello!
```

이를 XXD로 출력해보면 아래와 같은 결과가 나옴
정의된 바와 같이 Header끼리는 CRLF로 구분하고, Header와 Body는 두번의 CRLF로 구분
- Header와 Body 사이 end-of-line이 두번 발생하기 때문
```
$ curl -ki https://127.0.0.1:8443/hello | xxd
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100     7  100     7    0     0     51      0 --:--:-- --:--:-- --:--:--    51
0000000: 4854 5450 2f31 2e31 2032 3030 200d 0a44  HTTP/1.1 200 ..D
0000010: 6174 653a 2046 7269 2c20 3330 204d 6179  ate: Fri, 30 May
0000020: 2032 3032 3520 3032 3a30 373a 3036 2047   2025 02:07:06 G
0000030: 4d54 0d0a 436f 6e74 656e 742d 5479 7065  MT..Content-Type
0000040: 3a20 7465 7874 2f70 6c61 696e 3b63 6861  : text/plain;cha
0000050: 7273 6574 3d55 5446 2d38 0d0a 436f 6e74  rset=UTF-8..Cont
0000060: 656e 742d 4c65 6e67 7468 3a20 370d 0a0d  ent-Length: 7...
0000070: 0a48 656c 6c6f 210a                      .Hello!.
```


***References
- [RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](https://datatracker.ietf.org/doc/html/rfc2616#autoid-163)