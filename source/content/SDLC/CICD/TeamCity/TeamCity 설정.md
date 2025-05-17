TeamCity에서 VCS의 project를 가져와 작업하는 방법에 대해 정리한다.

TeamCity에서 VCS에 접속하기 위한 key를 생성한다.
```bash
ssh-keygen -t rsa -C "teamcity" -b 4096
```
생성된 private key를 VCS에 등록한다.

등록하기 위해서는, VCS에서 원하는 프로젝트에 들어가, Settings → Repository 에서 Deploy Keys 를 등록하면 된다. (적당한 이름을 주자) 이미 등록된 key가 있을 경우에는, 해당 key의 옆쪽의 Enable 버튼을 누르는 것으로 간단히 적용할 수 있다.

생성된 public key는 TeamCity 에 등록해 준다.
일단 Project를 생성하고, Project의 설정에서 SSH Keys 로 가면 public key를 등록할 수 있다.
그리고 git repository를 등록해 주어야 한다.
VCS Roots 메뉴에서 Create VCS root 를 눌러서 VCS 타입, 이름, ID, Fetch URL, Default Branch 등의 정보를 입력하여 준다.
- Type에는 Git을, Fetch URL에는 VCS 주소를 넣어주고, branch를 적용해 준다. (master, develop 등)

그 아래로 내려가면
![[Pasted image 20250517154335.png|500]]
인증 부분이 있으니, Uploaded Key 를 눌러 위에서 등록한 key를 선택해 주면 된다.
마지막으로 맨 아래의 Test connection 을 눌러 잘 동작하는 지 확인하고, 잘 되면 저장한다.
같은 프로젝트에 해당하는 여러 git repository 가 있으면 추가해 주면 된다. (별도의 Project를 만들 필요는 없다.)

git branch를 다 넣었으면, build 환경을 추가해 주면 된다.
왼쪽 메뉴의 맨 위의 General Settings를 누르면 Build Connection을 추가해 줄 수 있다.
Parent procet, Name 등을 넣어주고 Create를 누르면 된다.
이와 같이 Name 넣어주고 Create 를 누르면 된다.

그런 뒤 build 를 적용할 Git root 를 선택한다.
![[Pasted image 20250517154356.png|500]]

build 환경을 선택한다.
![[Pasted image 20250517154420.png|500]]
원하는 환경이 있으면 선택하면 되고, 없으면 위와 같이 command line 을 눌러 직접 명령을 이용할 수 있다.
- Custom Script에 원하는 빌드 구성 명령들을 넣어주면 된다
- 중간에 오류가 나면 빠져나올 수 있게 set -e 를 넣어줄 수 있다.

그리고 해당 git root 에 변화가 생기면 자동으로 동작할 수 있도록 Trigger 를 걸 수 있다.
![[Pasted image 20250517154443.png|500]]

마지막으로, 해당 Project 가 다른 project와 dependency가 있는 경우, Dependencies 에서 정의해 줄 수 있다. (dependency가 있는 project가 변하면 이 project도 재 build하게 된다.)