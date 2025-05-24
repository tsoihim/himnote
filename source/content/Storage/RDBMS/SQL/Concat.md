### Concat이란
여러 문자열, 열 값 등을 합쳐서 가져올 수 있도록 하는 명령
아래와 같이 테이블 레코드마다 가진 값들을 특정 문자열 포맷으로 합칠 수 있음
```sql
MariaDB [mysql]> select concat('Host is ', Host) from user;
+--------------------------+
| concat('Host is ', Host) |
+--------------------------+
| Host is %                |
| Host is %                |
| Host is localhost        |
| Host is localhost        |
| Host is localhost        |
+--------------------------+
5 rows in set (0.001 sec)
```

### GROUP_CONCAT
group_concat을 이용하면 일반적인 concat과 달리 concat으로 반환될 문자열들을 통으로 합칠 수 있음
- SEPARATOR 옵션까지 사용하면 각 레코드별 구분자도 따로 지정 가능
```sql
MariaDB [mysql]> select group_concat('Host is ', Host) from user;
+---------------------------------------------------------------------------+
| group_concat('Host is ', Host)                                            |
+---------------------------------------------------------------------------+
| Host is %,Host is %,Host is localhost,Host is localhost,Host is localhost |
+---------------------------------------------------------------------------+
1 row in set (0.001 sec)

MariaDB [mysql]> select group_concat('Host is ', Host SEPARATOR ' ') from user;
+---------------------------------------------------------------------------+
| group_concat('Host is ', Host SEPARATOR ' ')                              |
+---------------------------------------------------------------------------+
| Host is % Host is % Host is localhost Host is localhost Host is localhost |
+---------------------------------------------------------------------------+
1 row in set (0.001 sec)
```