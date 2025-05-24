## Hazelcast CLC란
Hazelcast Command-Line Client (CLC)란 Hazelcast용 커맨드라인 클라이언트
Go로 짜여있으며 바이너리 설치하여 바로 사용해볼 수 있음
- [https://github.com/hazelcast/hazelcast-commandline-client](https://github.com/hazelcast/hazelcast-commandline-client)
- [CLC 설치 가이드](https://docs.hazelcast.com/clc/5.3/install-clc)

## 설치 후 세팅방법
- [공식 설정 가이드 (to Platform)](https://docs.hazelcast.com/clc/5.3/connect-to-platform)

아래와 같은 접속 설정을 config.yaml을 파일명으로하여 ~/.hazelcast/configs/\[타겟 설정명] 디렉토리에 저장
```yaml
cluster:
	name: "<CLUSTER NAME>"
	address: "<MEMBER IP ADDRESS>"
	user: "<OPTIONAL USERNAME>"
	password: "<OPTIONAL PASSWORD>"
ssl:
	enabled: true
	server: "<HOSTNAME IN CLUSTER CERTIFICATE>"
```

이후 아래 명령을 통해 CLC에서 사용할 설정을 로드
`clc config add [타겟 설정명]`

이후 원하는 작업에 따라 아래와 같이 명령들을 수행하여 볼 수 있음
```bash
nara@nara-MacBook:~ clc object list
 Service Name | Object Name
 map          | default
 map          | jobMap
 map          | map

    OK Returned 3 row(s).
nara@nara-MacBook:~ clc map entry-set -n jobMap
    OK No entries found in Map 'jobMap'.
```