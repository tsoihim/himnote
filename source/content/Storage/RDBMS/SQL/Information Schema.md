INFORMATION_SCHEMA는 데이터베이스 메타데이타 정보에 대한 접근을 허용하여 데이터베이스 및 테이블명, 열의 타입 등을 조회할 수 있도록 해줌. 아래와 같은 쿼리들을 통해 DB의 여러 메타 정보를 확인할 수 있음

## 예제
### show tables
```sql
SELECT table_name 
FROM information_schema.tables 
WHERE table_schema = '[데이터베이스명]';
```

### drop all tables in database
1. 스크립트로 쓸 명령들 만들기
```sql
SELECT GROUP_CONCAT('DROP TABLE IF EXISTS ', table_name, ';' SEPARATOR '') 
FROM information_schema.tables 
WHERE table_schema = '[데이터베이스명]';
```
2. 스크립트 CLI에서 실행