인자주어 실행
- `java -jar build/libs/[ARTIFACT_NAME].jar --spring.config.additional-location=classpath:application-dev.yml --spring.profiles.active=dev`
- `java -Dlog4j.configurationFile=classpath:log4j2.yml,log4j2-syslog.yml -jar [ARTIFACT].jar --spring.config.additional-location=classpath:application-dev.yml --spring.profiles.active=dev`