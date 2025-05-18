### 개요
시스템 내 다른 컴포넌트들과 통신을 위해 아래와 같이 publisher 클라이언트를 작성해보았음
rabbitmq에서 제공하는 client 라이브러리를 사용
두 개의 Exchange를 사용 (notifications, logs)

### MQPub을 위한 클래스
```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class MQSender {
    private static final int RECOVERY_INTERVAL = 15000;
    // 사용할 Exchange
    private static final String EXCHANGE_0 = "notifications";
    private static final String EXCHANGE_1 = "logs";

    private String url;
    private Connection conn;
    private Channel channel;
		// 커스텀 정의한 Enum
    private State state;

    public MQSender() {
        this.url = getUrl();
        this.state = State.disconnected;
    }

    private String getUrl() {
        String url;
        String userName = "admin";
        String password = "admin";
        String hostName = "nara";
        int portNumber = 5672;
        String virtualHost = "nara-test";
        url = String.format("amqp://%s:%s@%s:%d/%s", userName, password, hostName, portNumber, virtualHost);
        return url;
    }

    public void start() {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setAutomaticRecoveryEnabled(true);
        factory.setNetworkRecoveryInterval(RECOVERY_INTERVAL);
        factory.setConnectionTimeout(RECOVERY_INTERVAL / 5);
        try {
            factory.setUri(url);
            conn = factory.newConnection();
            channel = conn.createChannel();
						// Exchange는 모두 broadcast로 사용
            channel.exchangeDeclare(EXCHANGE_0, "fanout", true);
            channel.exchangeDeclare(EXCHANGE_1, "fanout", true);
            this.state = State.connected;
        } catch (Exception e) {
            log.error("fail to create a RabbitMQ channel", e);
            this.state = State.disconnected;
        }
    }

    public void publish(String message, String exchangeName) {
        assert exchangeName == EXCHANGE_0 || exchangeName == EXCHANGE_1;

        try {
						// 아래와 같이 publish (라우트 키는 사용 안하므로 무시)
            channel.basicPublish(exchangeName, "", null, message.getBytes("UTF-8"));
            this.state = State.connected;
        } catch (Exception e) {
            log.error("fail to publish to the RabbitMQ channel", e);
            this.state = State.disconnected;
        }
    }

    public void stop() {
        try {
            channel.close();
            conn.close();
            this.state = State.disconnected;
        } catch (IOException e) {
            log.error("Error closing the rabbit MQ connection", e);
        } catch (TimeoutException e) {
            log.error("Timeout exception in closing the rabbit MQ connection", e);
        }
    }

    public State getState() {
        return this.state;
    }
}
```

### MQPub을 위한 일종의 Wrapper 클래스
```java
public class MQService {
    private MQSender sender;

    public synchronized void publish(String message, String exchangeName) {
        assert message != null;
        if (sender.getState().equals(State.connected)) {
            log.info("publishing msg " + message);
            sender.publish(message, exchangeName);
        } else {
            log.warn("not connected and omitted msg " + message);
        }
    }

    public synchronized boolean isConnected() {
        return sender.getState().equals(State.connected);
    }

    public synchronized void initializeProducer() {
        sender = new MQSender();
        sender.start();
    }
}
```