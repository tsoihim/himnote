### 명령어 종류
- DDL
    - CREATE, ALTER, DROP, TRUNCATE
- DML
    - SELECT, INSERT, DELETE, UPDATE
        - SELECT FROM
        - INSERT INTO
        - DELETE FROM
        - UPDATE SET
        - 번외
            - Like (_ : 한문자, % : 아무문자)
- DCL
    - GRANT, REVOKE
- TCL
    - COMMIT, ROLLBACK, CHECKPOINT

### SQL 명령 구성
- HAVING은 GROUP BY에서 씀
- NULL은 IS NOT과 사용

## 예제
### DML
```sql
# SELECT
SELECT 학과 FROM 학생;

SELECT DISTINCT 학과 FROM 학생;

// COUNT(컬럼)는 NULL 계산 안함
SELECT COUNT(DISTINCT 학과) FROM 학생 WHERE 학과='전산과';

// IN, ANY, ALL 숙지
SELECT id, name FROM ta_user WHERE id in (1, 2);
SELECT id, name FROM ta_user WHERE id = 1 or id = 2;

// GROUP BY 는 같은 값을 가진 행끼리 하나의 그룹으로 뭉쳐준다
SELECT 과목이름, MIN(점수) AS 최소점수, MAX(점수) AS 최대점수 FROM 성적 
GROUP BY 과목이름 HAVING AVG(점수)>=90;

SELECT ... FROM ... WHERE 이름 LIKE '이%' ORDER BY 이름 DESC;

// JOIN
SELECT ... FROM 학생정보 a JOIN 학과정보 b ON a.학과 = b.학과;
SELECT COUNT(*) CNT FROM T1 A CROSS JOIN T2 B WHERE A.NAME LIKE B.RULE; 

SELECT 제조사, 제품명, 단가
FROM 제품
WHERE 단가 > (SELECT SUM(단가) FROM 제품 WHERE 제조사='H');

# INSERT
INSERT INTO dbo.dept (deptno, dname, loc) 
VALUES (70, 'DBA TEAM', 'YEOKSAM'), (80, 'DESIGN TEAM', 'GANGNAM')

# UPDATE
UPDATE 테이블명 SET 컬럼=값 WHERE 점수>=90;

# DELETE
DELETE FROM 학생 WHERE 이름='민수';
```
### DDL
```sql
ALTER TABLE 학생 ADD 주소 VARCHAR(20);
```
### 내장함수
- MIN
- MAX
- COUNT
- AVG

### 기타
- WHERE에서 연산 순서는 NOT AND OR
- UNION은 중복 비허용 UNION ALL은 허용
- CREATE INDEX idx_name ON student(name);
