## 예제
아래 패키지에 Hazelcast에 CLI로 접근할 수 있는 ClientConsoleApp.class가 있음
이 클래스를 이용하여 hazelcast에 로컬에서 cli 클라이언트로 접근이 가능
`package com.hazelcast.client.console`

먼저 mvn 명령을 통해 아래 hazelcast jar 파일을 다운로드 받는다
`mvn dependency:get -Dartifact=com.hazelcast:hazelcast-all:[TARGET_VERSION]`

다운받은 jar 파일이 있는 경로에서 아래 명령을 수행
```bash
nara@nara-MacBook:Downloads java -cp hazelcast-all-4.2.8.jar com.hazelcast.client.console.ClientConsoleApp
11월 17, 2023 2:08:46 오후 com.hazelcast.internal.config.AbstractConfigLocator
INFO: Loading 'hazelcast-client-default.xml' from the classpath.
11월 17, 2023 2:08:47 오후 com.hazelcast.spi.discovery.integration.DiscoveryService
INFO: hz.client_1 [dev] [4.2.8] No discovery strategy is applicable for auto-detection
11월 17, 2023 2:08:47 오후 com.hazelcast.client.impl.spi.ClientInvocationService
INFO: hz.client_1 [dev] [4.2.8] Running with 2 response threads, dynamic=true
11월 17, 2023 2:08:47 오후 com.hazelcast.core.LifecycleService
INFO: hz.client_1 [dev] [4.2.8] HazelcastClient 4.2.8 (20230529 - 90df6cd) is STARTING
11월 17, 2023 2:08:47 오후 com.hazelcast.core.LifecycleService
INFO: hz.client_1 [dev] [4.2.8] HazelcastClient 4.2.8 (20230529 - 90df6cd) is STARTED
11월 17, 2023 2:08:47 오후 com.hazelcast.client.impl.connection.ClientConnectionManager
INFO: hz.client_1 [dev] [4.2.8] Trying to connect to cluster: dev
11월 17, 2023 2:08:47 오후 com.hazelcast.client.impl.connection.ClientConnectionManager
INFO: hz.client_1 [dev] [4.2.8] Trying to connect to [127.0.0.1]:5701
11월 17, 2023 2:08:47 오후 com.hazelcast.core.LifecycleService
INFO: hz.client_1 [dev] [4.2.8] HazelcastClient 4.2.8 (20230529 - 90df6cd) is CLIENT_CONNECTED
11월 17, 2023 2:08:47 오후 com.hazelcast.client.impl.connection.ClientConnectionManager
INFO: hz.client_1 [dev] [4.2.8] Authenticated with server [10.0.2.97]:5701:26564ea9-3683-4b4f-9f4e-a33fc5f9e49f, server version: 5.3.6, local address: /127.0.0.1:60540
11월 17, 2023 2:08:47 오후 com.hazelcast.internal.diagnostics.Diagnostics
INFO: hz.client_1 [dev] [4.2.8] Diagnostics disabled. To enable add -Dhazelcast.diagnostics.enabled=true to the JVM arguments.
11월 17, 2023 2:08:47 오후 com.hazelcast.client.impl.spi.ClientClusterService
INFO: hz.client_1 [dev] [4.2.8]

Members [1] {
	Member [10.0.2.97]:5701 - 26564ea9-3683-4b4f-9f4e-a33fc5f9e49f
}

11월 17, 2023 2:08:47 오후 com.hazelcast.client.impl.statistics.ClientStatisticsService
INFO: Client statistics is enabled with period 5 seconds.
hazelcast[default] >
```

아래와 같이 연결된 hazelcast 클러스터에서 데이터 받아올 수 있는 것 확인
```bash
hazelcast[default] > l.size
Size = 0
```

자세한 명령 체계는 help 명령 결과를 참고하면 됨

## 한계
데이터 구조별로 default들만 제공하고 여러 map, set 등에 대한 operation을 수행할 수 없음
따라서 Hazelcast CLC 사용을 권장