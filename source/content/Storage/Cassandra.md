### Overview
Apache Cassandra는 오픈소스 기반의 분산 NoSQL 스토리지
많은 열을 가지는 스토리지 모델이 최종적으로 정합성이 맞게 되는 방식으로 구현
대용량으로 낮은 지연율의 읽기/쓰기 작업이 필요한 어플리케이션에 맞게 설계됨

전반적으로 아래의 요구사항을 충족할 수 있도록 설계
- 멀티 마스터 데이터베이스 복제
- 저지연율의 가용성
- 스케일 아웃
- 파티션 키를 활용한 쿼리
- 유연한 스키마

### 스토리지 구조
**Keyspace**
- 테이블들의 집합 컨테이너와 같은 역할 
- 데이터를 여러 노드에 복제하는 전략을 포함
	- 데이터를 몇 개의 노드에 복제할지 결정하는 등
**Table**
- Row와 Column으로 구성
	- Column은 테이블 내 단일 데이터의 타입을 정의
- 테이블은 파티션 키의 열 기준으로 파티셔닝됨
- 다운타임없이 테이블에 언제든 새 열을 추가할 수 있음
**Partition**
- 파티션은 클러스터에서 행이 저장될 노드를 결정
**Row**
- 고유한 기본키로 구분될 수 있는 Column의 묶음
- 기본키는 Partition Key와 부가적인 Clustering Key로 구성됨
**Column**
- 타입을 거지는 단일 데이터로, Row에 속함 

아래 그림과 같이, 파티션 키인 COUNTRY에 따라 클러스터 내 노드에 테이블이 파티셔닝 되어 저장됨
![[Pasted image 20251203194006.png|500]]

### CQL
Cassandra는 SQL과 대동소이한 형태의 CQL을 제공
CQL을 통해 유저는 아래와 같은 요소를 이용하여 데이터 구성 가능
```SQL
CREATE TABLE t (
   id1 int,
   id2 int,
   c1 text,
   c2 text
   k int,
   v text,
   PRIMARY KEY ((id1,id2),c1,c2) -- (c1, c2는 클러스터링 키)
);
```


**References**
- https://cassandra.apache.org/doc/latest/cassandra/architecture/overview.html