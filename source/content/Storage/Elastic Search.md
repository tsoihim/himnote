### Elastic Search란
루씬을 기반으로 한 검색엔진

### **ES와 RDBMS 데이터 구조 비교**
RDBMS의 각 개념들은 아래와 같이 대응될 수 있음

| ES        | RDBMS     |
| --------- | --------- |
| Index     | Database  |
| Shard     | Partition |
| Type      | Table     |
| Document  | Row       |
| Field     | Column    |
| Mapping   | Schema    |
| Query DSL | SQL       |

Elastic Search는 **REST** API를 통해 데이터 조작을 지원, 이는 SQL과 아래와 같이 대응해볼 수 있음

| ES HTTP Method  | RDBMS SQL      |
| --------------- | -------------- |
| GET             | SELECT         |
| PUT             | INSERT         |
| POST            | UPDATE, SELECT |
| DELETE          | DELETE         |
| HEAD (인덱스 정보확인) |                |

### **역색인**
색인의 목적은 ‘문서의 위치’에 대한 index를 만들어서 빠르게 그 문서에 접근하고자 하는 것
역색인은 반대로 ‘문서 내의 문자와 같은 내용물’의 맵핑 정보를 색인해놓는 것
역색인은 검색엔진과 같은 문서의 내용의 검색이 필요한 형태에서 전문 검색의 형태로 주로 쓰임
- 일반 색인(forward index)은 책의 목차와 같은 의미
- 역색인(inverted index)은 책 가장 뒤의 단어 별 색인 페이지와 같은 의미
![[Pasted image 20250524224006.png|500]]

즉, 모든 단어가 어떤 document에 있는지 색인해두는 것을 역색인이라고 함
- [https://www.elastic.co/blog/found-elasticsearch-from-the-bottom-up](https://www.elastic.co/blog/found-elasticsearch-from-the-bottom-up)

### **ES의 특징과 장단점**
ES도 ****NoSQL의 일종으로 분류할 수 있고(스토리지로도 활용), 분산처리를 통해 실시간성으로 빠른 검색이 가능
특히 대량의 비정형 데이터 검색이 가능하며 전문 검색(full text) 검색과 구조 검색 모두를 지원
- 장점
    - 내용 전체를 색인해서 특정 단어가 포함된 문서를 검색 가능
    - 비정형 로그 데이터를 수집하여 통계 분석에 활용 가능 (ELK 연계 사용)
    - 정형화되지 않은 문서도 자동으로 색인하고 검색 가능
    - 상이한 인덱스일지라도 검색할 필드명만 같으면 여러 인덱스를 한번에 조회할 수 있음
    - 여러 계층 구조의 문서로 저장이 가능하며, 계층 구조로된 문서도 한번의 쿼리로 쉽게 조회 가능
    - 샤딩
- 단점
    - 내부적으로 commit과 flush같은 복잡한 과정을 거치기 때문에 느릴 수 있음
    - Transaction Rollback 지원하지 않음
    - 데이터의 업데이트를 제공하지 않음. 업데이트 명령이 올 경우 기존 문서를 삭제하고 새로운 문서를 생성
    - 7부터 라이센스가 바뀜

### **ES 기본용어**
![[Pasted image 20250524224049.png|500]]
하나의 Index는 복수의 Shard로 구성. Shard와 Lucene Index는 1:1로 맵핑
- Index
    - 데이터 저장 공간
    - 한 물리 노드에 여러 논리 인덱스 생성
    - 하나의 인덱스가 여러 노드에 분산 저장 (M:N)
- Shard
    - 색인된 문서는 하나의 인덱스 — 인덱스 내부에 색인된 데이터는 여러개의 파티션으로 나뉘어 구성
- Type
    - 인덱스의 논리적 구조
    - 6.1부터 인덱스당 하나의 타입만 설정 가능
- Document
    - 데이터가 저장되는 최소 단위 (JSON 포맷)
- Field
    - 문서를 구성하기 위한 속성
    - 하나의 필드는 목적에 따라 다수의 데이터 타입을 가짐
- Mapping
    - 문서의 필드, 필드 속성을 정의하고 그에 따른 색인 방법을 정의하는 프로세스
    - 스키마 정의 프로세스라고 보면 됨

### **Node Types**
- Master Node
    - 클러스터 관리
    - 노드 추가/제거 등 관리
- Data Node
    - 실질적인 데이터 저장 - 데이터가 실제로 분산 저장되는 물리 공간인 샤드가 배치됨
    - 검색과 통계 등 데이터 관련 작업 수행 - 색인 작업은 CPU, 메모리, 스토리 등 컴퓨팅 리소스를 많이 소요하므로 리소스 모니터링이 필요함.
- Coordinating Node
    - 사용자의 요청만 받아서 처리
    - 클러스터 관련 요청은 마스터노드로, 데이터 관련 요청은 데이터 노드로 전달
- Ingest Node
    - 문서의 전처리 담당
    - 인덱스 생성 전 문서의 형식을 다양하게 변경할 수 있음

### **Cluster, Node, Shard**
- 하나의 ES 클러스터는 인덱스의 문서를 조회할 때 마스터 노드를 통해 2개의 노드를 모두 조회해서 각 데이터를 취합한 후 결과를 하나로 합쳐서 제공
- 여러 클러스터를 연결해서 구성 가능. 클러스터 내 노드는 실시간으로 추가, 제거 가능
- 아래는 인덱스별로 샤드 2개, 리플리카 1개로 설정한 예시
```
{
    "settings" : {
        "index" : {
            "number_of_shards" : 2,
            "number_of_replicas" : 1        }
    }
}
```
위의 설정값이 서버에 적용되면 아래와 같은 형태가 됨

![[Pasted image 20250524224126.png|500]]
초록색 네모는 Primary shard, 투명한 네모는 Replica
**a** 인덱스를 예시로 보면, **a0**과 **a1** 두개의 shard로 구성되었으며, 이는 각각 node1과 node2에 저장되어 있음
**a0**과 **a1** shard의 replica는 각각 다른 node인 node4와 node3에 저장되어 있음

### **ES 주요 API**
- RESTful 방식의 API를 제공하며, 이를 통해 JSON 기반으로 통신한다.
- API 분류
    - 인덱스 관리 API (Indices API)
    - 문서 관리 API (Document API)
    - 검색 API (Search API)
    - 집계 API (Aggregation API)
- Schemaless 지원 기능
    - 인덱스의 존재 여부를 확인하고 인덱스가 존재하지 않는다면 문서를 분석해서 문서가 색인될 수 있도록 인덱스 자동 생성해주는 기능
    - 자동 생성된 필드를 보면 모든 필드가 text 타입과 keyword 타입을 동시에 제공하는 멀티필드 기능으로 구성되는 경우가 많다 -> 데이터 공간의 낭비 초래
    - 단순히 문자열로 저장하고 싶은 경우 keyword. 형태소 분석을 원하는 경우 text 타입 사용

### **인덱스 관리 API**
- 인덱스 생성
    - 인덱스 생성 시 맵핑이라는 세부설정을 이용할 수 있는데 맵핑은 문서와 문서에 포함된 필드, 필드 타입 등을 세세하게 지정하는 것이 가능한 설정 방식
    - 주의 : 한번 생성된 맵핑 정보는 변경 불가. 잘못 생성했다면 데이터 삭제하고 다시 색인해야함
```
PUT /my-index-000001
{
  "settings": {
    "index": {
      "number_of_shards": 3,
      "number_of_replicas": 2
    }
  }
}
```
- Index API: [](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html)[https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)

### **Document 관리 API ( = single document api)**
- 실제 Documnent 조회, 수정, 삭제
    - 기본적으로 search api를 제공하나 색인된 문서의 ID 기준으로 한건 한건의 문서를 다룰 경우 document 관리 api를 이용
        - index api : 한건의 문서를 색인
        - get api : 조회
        - delete api : 삭제
        - update api : 업데이트
- multi-document api도 제공
    - multi get api : 다수 문서 조회
- ID 지정하지 않고 문서 생성도 가능 (UUID를 통해 무작위 생성)
    - 단, 검색엔진은 DB와 주기적 동기화가 필요하므로 random 식별자로 사용한 경우 DB 맵핑을 위해 별도의 룩업테이블이 필요함. 따라서 처음에 생성할 때 부터 DB의 식별자와 맞춰주는 것이 중요
```
POST my-index-000001/_doc/
{
  "@timestamp": "2099-11-15T13:12:00",
  "message": "GET /search HTTP/1.1 200 1070000",
  "user": {
    "id": "kimchy"
  }
}
```
- Document API:[https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html)

### **검색 API**
- 검색 API 사용방식은 크게 두가지
    - HTTP URI 형태의 파라미터를 URI에 추가해 검색
    - RESTful API 방식인 Query DSL
        - RESTful Request 방식이 URI 방식보다 제약사항이 적으므로 더 선호됨
```
GET /my-index-000001/_search
{
  "query": {
    "term": {
      "user.id": "kimchy"
    }
  }
}
```
- Search API:[https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html)

### **집계 API**
- 버킷 구조 안에 다른 그룹화된 데이터가 포함되어 있음 → 집계 API가 강력한 이유중 하나 : 버킷 안에 다른 버킷의 결과를 추가할 수 있다. 다양한 집계 유형을 결합, 중첩, 조합이 가능


**References**
- https://jaemunbro.medium.com/elastic-search-%EA%B8%B0%EC%B4%88-%EC%8A%A4%ED%84%B0%EB%94%94-ff01870094f0