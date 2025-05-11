## Abstract
아래 링크 참고하여 Ubuntu 20.04가 설치된 서버에서 OpenStack 설치 진행
- [https://docs.openstack.org/install-guide/index.html](https://docs.openstack.org/install-guide/index.html)
    - 가이드는 Antelope 기준으로 확인
- 타겟 OpenStack 버전은 Yoga, 원 노드 설치
    - [https://releases.openstack.org/yoga/index.html](https://releases.openstack.org/yoga/index.html)

## Installation
### [Prerequisite](https://docs.openstack.org/install-guide/environment.html)
- security
    - 모든 PASS는 atto1234 통일
- Network
    - controller를 서버 본인 IP로 hostname 등록
- OpenStack package
    - repo 추가 및 OpenStack Client 추가
    - [admin-openrc.sh](https://docs.openstack.org/keystone/2023.1/install/keystone-openrc-ubuntu.html) 설정
- MariaDB, RabbitMQ, Memcached 설치
- Etcd 설치는 스킵
### [Keystone](https://docs.openstack.org/keystone/2023.1/install/index-ubuntu.html)
- 21.0.1 버전
- DB 세팅
- Keystone 패키지 설치
- conf 설정
- Bootstrap, 웹 서버 세팅
- Domain, Project, User 설정
- openstack 명령어 통해 토큰 발급받아 확인
### [Glance](https://docs.openstack.org/glance/2023.1/install/)
- 24.2.1 버전
- DB 세팅
- Credential, Service 설정
- Glance 패키지 설치 및 conf 설정
- glane 명령어 통해 image 업로드하여 확인
\*Quota는 스킵
### [Placement](https://docs.openstack.org/placement/2023.1/install/)
- 7.0.0 버전
- DB 세팅
- Service 설정
- Placement 패키지 설치 및 conf 설정
- Placement API 이용하는 명령어 실행하여 확인
### [Nova](https://docs.openstack.org/nova/2023.2/install/)
- 25.2.1 버전
- Control 노드 설정 선행 후 Compute 노드 설정 수행
- DB 세팅
- Service 설정
- Nova 패키지 설치 및 conf 설정
    - 이후 Neutron Section 추가 필요
- nova-status 명령어 실행하여 확인
### [Neutron](https://docs.openstack.org/neutron/2023.2/install/)
NIC 두개 사용, MGMT용 하나와 Provider용 하나를 OVS에 연결
- 25.2.1 버전
- DB 세팅
- Service 설정
- Control 노드 설정 선행 후 Compute 노드 설정 수행
- Provider Network로 진행
- Neutron 패키지 설치 및 conf 설정
- 나머지 설정 및 설치 마무리
    - Nova 설정도 업데이트
- [Provider 네트워크 생성](https://docs.openstack.org/ocata/networking-guide/deploy-ovs-provider.html)
- Compute 노드 설정 마무리
- openstack network 명령 실행하여 확인
### [Horizon](https://docs.openstack.org/horizon/2023.2/install/)
- 22.1.1 버전
- Horizon 패키지 설치 및 conf 설정
- [http://controller/horizon](http://controller/horizon) 로 접근하여 웹서버 확인
- 짠!
![[Pasted image 20250511131833.png|500]]
![[Pasted image 20250511131840.png|500]]

### 주의사항
- API 호출을 위한 URL 작성 시 TLS 적용 여부나 포트를 잘 확인할 것
- 네트워크 설정은 정말 다양하게 할 수 있음
- 관리 보안 그룹 규칙을 추가 설정하지 않으면 인입되는 패킷은 필터링됨
    - 다른 프로토콜, IP 프로토콜 값을 -1로 주면 모든 인입되는 패킷 허용