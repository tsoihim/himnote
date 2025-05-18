Bitbucket은 github action과 비슷한 pipeline을 지원한다.

Pipelines 탭에서 세팅을 할 수 있고, Source 트리에 생긴 bitbucket-pipelines.yml을 작성하여 원하는 동작을 지정할 수 있다.
- yml에 지정한 내용은 bitbucket 클라우드 컨테이너 안에서 실행되니 명확한 이미지 지정은 필수
- 본인의 경우 필요한 의존성과 특수한 환경이 필요하여 그냥 빌드 환경을 도커 허브에 올리고 이를 지정해줌

간단한 유틸 프로젝트를 작성하였는데, 해당 코드를 빌드하여 실행 가능파일로 만든 후 해당 파일들을 이용하여 rpm 및 deb 패키지로 변환하는 스크립트를 pipeline을 이용하여 실행하였다.

bitbucket-pipelinse.yml 파일에 아래와 같이 일련의 pipeline들을 작성하기만 하면 된다. 또한 pipeline 완료 후 다운로드 받아야할 산출물이 있는 경우 artifact에 지정해두면 된다.
```yaml
pipelines:
  default:
    - step:
        name: 'Build'
        image: hanhimnara/asw:build
        script:
          - pwd
          - ls -al
					...
					...
					...
        artifacts: 
          - pkg/**
```

또한, 커밋 이벤트로 빌드 트리거링 시켜서 산출물이 계속 생성되도록 할 수 있으니 참고.