## RabbitMQ란
Advanced Messaging Queueing Protocol (AMQP)라는 표준 프로토콜을 구현한 오픈소스 메시지 브로커

## 구조
아래와 같은 컴포넌트들이 존재
- Exchange : Publisher로부터 수신한 메시지를 큐에 분배하는 라우터 역할
    - Direct : 바인딩 된 큐 중 라우팅 키가 매핑된 큐로 전달
    - Fanout : 라우팅 키와 상관없이 바인딩된 모든 큐에 전달
    - Topic : 바인딩 된 큐 중 라우팅 키가 패턴에 맞는 큐에 모두 전달
    - Headers : 메시지 헤더에 여러 속성들을 더해 매칭되는 큐에 메시지 전달
- Routing Key - 메시지를 큐로 라우팅하는 방법을 결정할 때 Exchange가 참조하는 메시지 속성
- Binding - Exchange와 Queue를 연결해주는 역할
- Queue - 메시지를 최종적으로 담고 있다가 Consumer에게 전달
```bash
		                      Broker
Producers -> [Exchange -- Binding --> Queue] -> Consumers
```
![[Pasted image 20250517200147.png|500]]

## 사용해보기
- 도커 컨테이너로 실행
```bash
# 이미지 pull
dev@nara-MacBook:~ docker pull rabbitmq
Using default tag: latest
latest: Pulling from library/rabbitmq
445a6a12be2b: Pull complete
af80632035ec: Pull complete
...
Digest: sha256:50282e8cbf22ac261efd13fb881da900c09367ceb166c3ec5d23089986464df6
Status: Downloaded newer image for rabbitmq:latest
docker.io/library/rabbitmq:latest

# 컨테이너 실행
dev@nara-MacBook:~ docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq
90bce3a854c62496b1fee5d78ecf83c7825f704581011dfc19dac6d7eba11630

dev@nara-MacBook:~ docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED         STATUS        PORTS                                                                                              NAMES
90bce3a854c6   rabbitmq   "docker-entrypoint.s…"   2 seconds ago   Up 1 second   4369/tcp, 0.0.0.0:5672->5672/tcp, 5671/tcp, 15691-15692/tcp, 25672/tcp, 0.0.0.0:15672->15672/tcp   rabbitmq

# mgmt 플러그인 적용
dev@nara-MacBook:~ docker exec rabbitmq rabbitmq-plugins enable rabbitmq_management
Enabling plugins on node rabbit@90bce3a854c6:
rabbitmq_management
The following plugins have been configured:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_prometheus
  rabbitmq_web_dispatch
Applying plugin configuration to rabbit@90bce3a854c6...
The following plugins have been enabled:
  rabbitmq_management

started 1 plugins.
```
- 15672 포트로 mgmt GUI에 접속하여 아래와 같은 화면 확인
    - guest/guest로 접속
    ![[Pasted image 20250517200245.png|500]]
- Exchange와 Queues에서 각각을 정의하고 바인딩할 수 있음


**References**
- https://magpienote.tistory.com/230
- https://cheese10yun.github.io/spring-rabbitmq/