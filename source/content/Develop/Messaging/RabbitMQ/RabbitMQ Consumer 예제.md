## 개요
OpenStack은 내부 AMQP로 RabbitMQ를 기본값으로 사용
- Nova, Cinder 등 내부 서비스들은 이를 통해 서로 통신하며 각자의 역할을 수행

본 예제에서는 OpenStack에서 Volume을 생성하고, 그에 상응하는 메시지를 별도의 Java 프로세스에서 수신할 수 있도록함

## 환경
OpenStack
- Caracal 버전
- Cinder 포함
- RabbitMQ 사용

RabbitMQ
- Exchange (_openstack_)
    - Topic 타입 사용 (Routing Key 매칭)
- Queue (_mq-test_)
    - exclusive, auto-delete로 만들어서 사용
Java Runtime

## 코드
### Gradle
build.gradle에 아래와 같이 amqp-client 의존성 추가
```groovy
dependencies {
		...
    implementation 'com.rabbitmq:amqp-client:5.23.0'
		...
}
```
### Java
```java
import java.io.IOException;
import java.util.concurrent.TimeoutException;

import com.rabbitmq.client.AMQP;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.Consumer;
import com.rabbitmq.client.DefaultConsumer;
import com.rabbitmq.client.Envelope;

public class AMQPConsumer {

    private static final String HOST = "[RABBITMQ_IP]";
    private static final int PORT = 5672;
    private static final String USERNAME = "openstack";
    private static final String PASSWORD = "[RABBITMQ_PASSWORD]";

    private static final String EXCHANGE = "openstack";
    private static final String ROUTING_KEY1 = "cinder-volume.caracal1@lvm-1";
    private static final String ROUTING_KEY2 = "cinder-volume.caracal1@lvm-1.caracal1";
    private static final String QUEUE = "mq-test";

    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost(HOST);
        factory.setPort(PORT);
        factory.setUsername(USERNAME);
        factory.setPassword(PASSWORD);

        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        channel.queueDeclare(QUEUE, false, true, true, null);
        channel.queueBind(QUEUE, EXCHANGE, ROUTING_KEY1);
        channel.queueBind(QUEUE, EXCHANGE, ROUTING_KEY2);

        Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties,
                    byte[] body) throws IOException {
                String message = new String(body, "UTF-8");
                System.out.println("\\n<NEW MESSAGE RECEIVED>\\n" + message);
            }
        };

        channel.basicConsume(QUEUE, true, consumer);
    }
}
```
### 실행 결과
위 코드 실행 후 _Connections_ 탭에서 아래와 같이 연결 정보가 추가되고 상응하는 채널도 추가된 것 확인 가능
![[Pasted image 20250517200404.png|500]]

볼륨 생성 시 아래와 같이 메시지 출력됨
```
<NEW MESSAGE RECEIVED>
{"oslo.version": "2.0", "oslo.message": "{\\"method\\": \\"create_volume\\", ...}                                                  
```