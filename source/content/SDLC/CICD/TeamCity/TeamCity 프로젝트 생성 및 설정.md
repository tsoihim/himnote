## 개요
- TeamCity 이용하여 사내 프로젝트 CI 구성한 방식 서술
    - 따라서 민감한 내용들은 기재하지 않음

## 목표
- 개발자가 손수 프로젝트를 빌드하는 비효율을 제거
- 구성원들이 프로젝트 산출물을 지정된 위치에서 가져갈 수 있도록 함
- 빌드 혹은 최소 수준의 동작 실패를 커밋 직후 자동으로 확인

## 필요 기능
- 커밋 단위로 프로젝트 빌드
- 브랜치 입력받아 프로젝트 내 모든 VCS에서 해당 브랜치로 체크아웃 후 빌드
- 빌드 실패 시 이메일로 담당자에게 전달
- 빌드 후 artifact 생성 (필요한 경우 특정 서버로 전달 및 CD 등 수행)

## **TeamCity Agent 생성·설정**
프로젝트 빌드를 위해서 ubuntu 16.04와 CentOS 7.6 기반의 빌드 환경이 필요
ubuntu의 경우 teamcity에서 제공하는 docker 이미지 사용 (ubuntu 16.04를 사용하고 있는 2017.2.4 버전)
CentOS의 경우 직접 도커 이미지 만들었음
- teamcity 서버의 /opt/teamcity/agent-sample 디렉토리를 만들어 필요한 설정 추가
```bash
root@osm:/opt/teamcity# cat agent-sample/buildAgent.properties## TeamCity build agent configuration file#######################################   Required Agent Properties        ######################################### The address of the TeamCity server. The same as is used to open TeamCity web interface in the browser.## Example:  serverUrl=https://buildserver.mydomain.com:8111
serverUrl=192.168.10.1\\:8111

## The unique name of the agent used to identify this agent on the TeamCity server## Use blank name to let server generate it.## By default, this name would be created from the build agent's host name
name=agent-for-sample
```
- 위와 같은 설정으로 agent-for-sample 에이전트 생성 후 프로젝트에 할당한 sample agent pool에 등록
- 이미지 생성, 커밋 및 적용 방법에 대한 자세한 내용은 디렉토리 내 타 문서 참고

## **프로젝트 생성·설정**
### 프로젝트
- 프로젝트 생성
    - OS 별로 각각 빌드 구성을 두었음 (각 빌드 구성은 사용하는 agent 및 artifact 명 외엔 모든 설정 동일)
- VCS 등록 (repos)
    - 서브모듈은 checkout 안하고 무시하도록 함 (현재 버전별로 서브모듈에도 동일한 명 브랜치 따서 작업하기 때문)
- Build Step 등록
    - 실제 빌드 스텝
    - Artifact 만들기 위한 패키징 스텝
    ![[Pasted image 20250517154627.png|500]] 
- 빌드 트리거는 git commit 단위로 지정
    - 여러 repo가 OBelle Access 코어 프로젝트에 관여하므로 커밋을 모아서 빌드할 수 있도록 quiet mode 3분을 vcs trigger에 지정
    ![[Pasted image 20250517154646.png|500]]
- 빌드 구성을 OS별로 분리했으므로 자기에게 할당된 OS에 맞는 빌드 에이전트를 사용하도록 설정
- 빌드가 성공적인 경우 dist-\[OS명].tar.gz로 artifact를 지정
- 과거 빌드 데이터의 경우 3일 지나면 전부 삭제되도록 하였음
    - clean-up 주기는 추후 상황에 맞게 재설정할 수 있음
- 계정은 두 개 그룹으로 분리
    - Admin : 프로젝트 관리 권한을 가짐
        - Users: core devs
    - User : 빌드 실행 및 프로젝트 보기 권한을 가짐
        - Users: 팀 공용 계정


**References**
- https://www.jetbrains.com/help/teamcity/teamcity-documentation.html
- https://unixcop.com/install-and-configure-teamcity-agent-on-linux/