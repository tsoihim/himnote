## 개요
적은 수의 스레드로 동시성을 처리하기 위한 리액티브 웹 프레임워크
비동기-논블로킹을 통한 효율적인 요청 처리가 핵심

## 특징
#### 동시성 모델
- WebFlux는 Event Loop 모델로, 소수의 워커 스레드가 이벤트를 처리
	- I/O 작업 시 스레드를 점유하지 않고 다음 요청을 처리
- MVC는 요청마다 스레드가 발생
#### 스택 비교
- MVC와 달리 Tomcat 대신 Netty를 사용
- Java 함수형 인터페이스를 활용하여 선언형으로 프로그래밍함
	- 함수형 연산자를 통해 데이터 파이프라인을 구축하는 것
		- flatMap 등

## 구조
#### 키워드
**Pipeline**
- 데이터를 어떻게 가공 및 처리할지 연산자(`map`, `filter`, `flatMap` 등)를 체인 형태로 엮어놓은 명세서
- 실제 데이터가 흐르기 전까지는 실행되지 않는 '비활성(Lazy)' 상태로 존재, 요청이 들어올 때마다 메모리에 생성
**Pub/Sub**
- Pub : 데이터를 생성하거나 외부(DB, API)에서 가져와 파이프라인에 밀어 넣어주는 주체 (`Flux`, `Mono`)
- Sub : 파이프라인의 끝에서 최종 데이터를 받아 처리하는 주체 (Netty 엔진)
- 구독이 일어나는 순간 발행자의 데이터가 파이프라인을 타고 흐르기 시작
**Event Loop**
- 적은 수의 스레드로 수많은 이벤트를 끊임없이 탐지하고 처리하는 무한 루프
- 파이프라인의 각 단계(Job)를 워커 스레드에게 배분
	- 비동기 I/O 작업이 끝나면 발생하는 이벤트를 감지, 멈췄던 파이프라인의 다음 단계를 다시 실행하도록 스레드를 깨움
#### 동작 흐름
- 이벤트 루프 그룹을 구성, 클라이언트 연결을 소수의 스레드가 감시
- 데이터가 준비된 채널이 생기면 셀렉터가 감지
	- 데이터 처리 방식이 정의된 파이프라인을 이벤트 루프에 전달 (subscribe)
		- 파이프라인 내 체이닝된 각 단계의 연산자가 모두 발행자 역할을 함
- 이벤트 루프 내의 워커 스레드가 파이프라인에 정의된 작업을 수행
	- 작업 내에 비동기 I/O 등의 작업이 있는 경우, 워커는 기실행한 작업 정보를 등록 후 즉시 다른 작업을 수행
	- I/O가 완료되면 나머지 작업은 이벤트 루프내 유휴 워커에 의해 실행
- 모든 연산자가 처리를 마치면 구독자인 Netty 엔진에 결과값 전달됨
- 최종 결과가 클라이언트에게 전달, 파이프라인 정보 등은 GC 처리됨

## 예제
아래 프로젝트에 [[Armeria]]와 결합한 WebFlux CRUD 예제를 구현하였음
- [tsoihim/armeria-webflux](https://github.com/tsoihim/armeria-webflux)

Mono, Flux를 자유자재로 다루는 것이 중요함 (Optional과 Stream API에 익숙하다면 좋음)
- 상황에 따라 just, error, empty, fromCallable 등의 메소드 사용

Controller, Service, Repository 등의 Stereotype은 그대로 가져가되, 각 메소드 구현 시 함수형 연산자를 사용하며, Flux나 Mono를 반환하도록 해야함
#### Controller
```Java
public class DeviceController {

    private final DeviceService deviceService;

    @GetMapping("/devices")
    Flux<DeviceDTO> getDevices() {
        return deviceService.fetchDevices();
    }

    @PostMapping("/devices")
    Mono<DeviceDTO> createDevice(@RequestBody DeviceDTO.DeviceCreateDTO createDTO) {
        return deviceService.createDevice(createDTO);
    }

    @DeleteMapping("/devices/{deviceId}")
    Mono<Void> deleteDevice(@PathVariable Long deviceId) {
        return deviceService.deleteDevice(deviceId);
    }

}
```
#### Service
- 변환 로직이 Mono, Flux를 포함하면 flatMap, 그렇지 않다면 map
	- Nested 구조를 피하기 위함; Optional의 그것과 비슷
``` Java
public class DeviceService {

    private final DeviceRepository deviceRepository;
    private final ObjectMapper objectMapper = new ObjectMapper();

    public Flux<DeviceDTO> fetchDevices() {
        return deviceRepository.findAll()
                .map(v -> objectMapper.convertValue(v, DeviceDTO.class));
    }

    public Mono<DeviceDTO> createDevice(DeviceDTO.DeviceCreateDTO createDTO) {
        return deviceRepository.save(new Device(null, createDTO.getName()))
                .map(v -> objectMapper.convertValue(v, DeviceDTO.class));
    }

    public Mono<Void> deleteDevice(Long id) {
        return deviceRepository.findById(id)
                .switchIfEmpty(Mono.error(new ResponseStatusException(HttpStatus.NOT_FOUND)))
                .flatMap(v -> deviceRepository.deleteById(id));
    }

}
```
#### 주의사항
- DB 등 I/O 작업 시 비동기 I/O가 일어날 수 있도록 알맞은 클라이언트 사용해야함 
	- JDBC는 블로킹이라 사용 X 대신, R2DBC 사용
- 가능하면 전구간 Async-NonBlocking으로 구현
	- 블로킹 작업을 위한 고정 스레드 풀을 따로 두는 기능도 있으나 최대한 지양
	- 적은 수의 스레드를 사용하는데 블로킹 되어버리면 WebFlux 안쓰느니만 못함



**References**
- [Overview :: Spring Framework](https://docs.spring.io/spring-framework/reference/web/webflux/new-framework.html)