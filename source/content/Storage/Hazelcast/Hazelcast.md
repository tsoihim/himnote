## Hazelcast란
분산 연산 및 데이터 저장소 역할을 하는 인메모리 데이터 그리드로 자원 효율적인 실시간 어플리케이션을 만들 수 있도록 해줌. 네트워크로 클러스터링 맺은 노드끼리 데이터를 프로세스할 수 있도록 함 (peer to peer).

JAVA로 짜여진 오픈소스 프로젝트로, JVM 환경에서 Embedded하여 사용하거나 hibernate도 사용 가능
- [https://github.com/hazelcast/hazelcast](https://github.com/hazelcast/hazelcast)

## 실행 방법 (Embedded way)
SpringBoot + Gradle 환경 기준, 아래와 같이 hazelcast 의존성을 build.gradle에 추가
`implementation 'com.hazelcast:hazelcast:[TARGET_VERSION]'`

src/main/resources 경로에 hazelcast.xml을 추가
- [Hazelcast Default XML](https://github.com/hazelcast/hazelcast/blob/master/hazelcast/src/main/resources/hazelcast-default.xml)

빌드 후 실행하여 Hazelcast가 정상 실행되는 것 확인
```bash
2023-11-17 15:53:04.126 main [INFO ] com.hazelcast.system.logo.log() 65 - [169.254.59.44]:5701 [dev] [5.3.6] 
        +       +  o    o     o     o---o o----o o      o---o     o     o----o o--o--o
        + +   + +  |    |    / \\       /  |      |     /         / \\    |         |   
        + + + + +  o----o   o   o     o   o----o |    o         o   o   o----o    |   
        + +   + +  |    |  /     \\   /    |      |     \\       /     \\       |    |   
        +       +  o    o o       o o---o o----o o----o o---o o       o o----o    o   
2023-11-17 15:53:04.126 main [INFO ] com.hazelcast.system.log() 65 - [169.254.59.44]:5701 [dev] [5.3.6] Copyright (c) 2008-2023, Hazelcast, Inc. All Rights Reserved.
2023-11-17 15:53:04.126 main [INFO ] com.hazelcast.system.log() 65 - [169.254.59.44]:5701 [dev] [5.3.6] Hazelcast Platform 5.3.6 (20231109 - 9903dc9) starting at [169.254.59.44]:5701
```
이를 통해 Spring Boot 서비스, 컨트롤러 등에서 @Autowired 통하여 Hazelcast 인스턴스를 가져갈 수 있음

## 분산 데이터 구조 (자료 구조)
Hazelcast는 다양한 유형의 분산 데이터 구조를 지원. 아래는 그 중 일부
### MAP
키-값 쌍들로 이루어진 데이터 구조
### Queue
한 쪽에서 아이템을 추가한 후 다른 곳에서 pop하여 쓰는 것을 지원하는 데이터 구조
### MultiMap
한 키에 여러 값들을 저장하는 것을 지원하는 데이터 구조
### Set
중복된 element를 허용하지 않고, 그들의 순서를 보장하지 않는 데이터 구조
### List
Set과 비슷하나, 중복을 허용하며 그들간의 순서를 보장하는 데이터 구조

이외에도 Topic, ISemaphore 등 다양한 데이터 구조 지원

## TIP
- JAVA 오브젝트를 Hazelcast Java API 이용하여 Key, Value 쌍으로 Map에 그대로 밀어넣으면 나중에 반환도 오브젝트 그대로 marshalling/unmarshalling해서 줌