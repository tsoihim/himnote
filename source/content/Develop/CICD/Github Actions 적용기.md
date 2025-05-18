## 개요
> GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

## 목표
Django로 REST API를 통해 간단한 CRUD를 구현한 아래 repository에 Actions 적용, 단위 테스트 수행
- https://github.com/tsoihim/zoro

## Actions 구성 요소
Repository에서 **event**가 발생할 때 Actions **workflow**가 작동하도록 설정해아함
Workflow는 여러 **job**을 가질 수 있으며 각 **job**은 가상 머신이나 컨테이너와 같은 가상환경인 **runner** 내부에서 순차적으로, 혹은 병렬적으로 실행됨
각 job은 여러 step을 가질 수 있으며, 각 step에서는 커스텀 스크립트나 action(재활용 가능한 동작)이 실행됨
### Workflows
여러 job을 수행하는 자동화된 프로세스
yml 파일로 정의할 수 있으며 repo의 `.github/workflows` 에 저장됨
### Events
Workflow를 트리거링하는 repository에서 발생한 특수한 이벤트
pull request, 이슈 생성 혹은 push 등의 예가 있음
### Jobs
같은 runner에서 실행되는 step들의 집합으로, 각 step은 shell script이거나 action임
여러 스텝을 활용해서, 빌드 스텝 이후 테스트 스텝이 후행하도록 하는 등의 작업 가능
- 같은 runner에서 실행되므로 데이터 공유 등도 가능하므로 서로 간에 의존성 줄 수 있음
### Actions
Action은 복잡하고 반복적인 일을 처리하도록 만들어진 Github Actions내의 어플리케이션
커스텀하게 제작 가능하며, Marketplace에서 찾아 쓰는 것도 가능
### Runners
Runner는 workflow가 실질적으로 수행되는 서버, 각 runner는 동시에 한 job만 수행함
기본적으로 Ubuntu, Windows, macOS 지원

## 적용 예제
Github Repo에서 Actions 메뉴로 진입하면 친절하게도 해당 프로젝트에 적합한 workflow를 추천해줌

![[Pasted image 20250517153053.png|600]]

좌측의 Django를 선택하면 아래와 같이 Django 테스트를 실행시켜주는 yml을 생성하여줌
- 기본 저장 위치는 .github/workflows 디렉토리 하위
- 우선, 자동으로 생성해준 파일을 조금 수정하여 적용해보았음
```yaml
name: Django CI # Workflow 이름

on: # Event 정의
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

jobs: # Job 정의
  build:

    runs-on: ubuntu-latest # Runner 정의
    strategy:
      max-parallel: 2
      matrix:
        python-version: [3.9]

    steps:
    - uses: actions/checkout@v4 # action 사용
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v3
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install Dependencies
      run: | # custom script 사용
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Run Tests
      run: |
        cd zoro_server
        python manage.py test

```
- public repo는 무료로 사용 가능하므로 triggering 조건(on)은 따로 수정하지 않았음

설정 완료하고 나면 yml 파일이 repo에 추가되고 Actions 메뉴에서 아래와 같이 workflow 등록 확인 가능
![[Pasted image 20250517153145.png|600]]

Repo에 push가 발생할 때마다 아래와 같이 workflow가 호출된 것을 확인할 수 있음
![[Pasted image 20250517153158.png|600]]
상기 결과를 보면, 실패한 workflow도 있는데 이 경우 Github 알람 설정에 따라 mail로 알람을 받을 수 있음

또한 각 workflow의 결과는 아래와 같이 상세 조회가 가능함
- zoro 프로젝트엔 아직 test를 추가하지 않았는데, 따라서 workflow 성공 시 아래와 같은 결과 확인 가능(Django 프로젝트에서 `python manage.py test` 명령을 실행한 결과)
![[Pasted image 20250517153212.png|600]]


**References**
- https://docs.github.com/ko/actions