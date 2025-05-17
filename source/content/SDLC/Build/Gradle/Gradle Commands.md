| cmd                              | feature                                                                                     |
| -------------------------------- | ------------------------------------------------------------------------------------------- |
| `gradle test`                    | 단위 테스트 수행                                                                                   |
| `grade build -x test`            | 단위 테스트 스킵 후 빌드                                                                              |
| `gradle :subproject:test`        | 하위프로젝트 단위 테스트 수행                                                                            |
| `gradle test --tests "[FILTER]"` | 특정 테스트 수행                                                                                   |
| `gradle bootRun`                 | 애플리케이션 실행 (`./gradlew build && java -jar ./gate/build/libs/gate-spring-0.0.1-SNAPSHOT.jar`) |
| `gradle -q dependencies`         | 프로젝트 의존성 확인                                                                                 |
| `gradle publishToMavenLocal`     | 플러그인 통해 maven local repo에 publish                                                           |
