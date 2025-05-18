## Artifact Repository
Maven Repository는 build artifact와 의존성 저장소

Repo에는 크게 local, remote 두 종류가 있음
1. local repo는 mvn이 동작하는 머신의 디렉토리. remote에서 다운로드 한 것을 캐싱하거나 아직 릴리즈하지 않은 임시 아티팩트 등을 보관하는 용도로 사용
    1. OS별 local repo 경로
        1. Windows: C:\Users\<User_Name>\.m2Copy
        2. Linux: /home/<User_Name>/.m2Copy
        3. Mac: /Users/<user_name>/.m2
2. remote repo는 그 외 http 등으로 원격 접속되는 모든 repository를 통칭. 인터넷을 통해 모두가 내려받을 수 있도록 artifact를 release할 수도 있고 폐쇠된 네트워크에서 팀간 artifact 전달 등에도 사용할 수 있음

일반적으로 local repo는 디스크 공간이 모자른 경우 알아서 지워지도록 되어있으므로 크게 신경쓸 것이 없음
remote 통해서는 권한만 있다면 articact를 upload/download 모두 가능


**References**
- https://maven.apache.org/guides/introduction/introduction-to-repositories.html