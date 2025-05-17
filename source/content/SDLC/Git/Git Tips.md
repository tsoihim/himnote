### 유용한 설정
- git config --global user.email \[your email addr]
- git config --global user.name \[your name]
- git config --global core.editor vim
    - commit log 작성 편집기를 vim으로
- git config --global push.default current
    - git push 할 때 현재 브랜치만 push. 안 그러면 변경이 있는 다른 브랜치까지 push하다가 conflict 때문에 거부되는 경우가 있어서 불편
- git config --global remote.pushdefault myfork
    - git push 명령의 default target remote를 myfork로 만듬 (아래 '개발' 절 참고)
- git config --global color.ui true
    - status, diff 등의 텍스트가 칼라로 나오기 때문에 가독성이 매우 좋음
- alias 설정: 자주 쓰는 git 명령어 축약 (예: git status 대신에 git st라고 쓸 수 있음)
    - git config --global alias.st status
    - git config --global alias.br branch
    - git config --global alias.co checkout

### 표기법
- HEAD 표기
    - 최근 커밋이면 HEAD, 1개 커밋 전이면 HEAD^ 와 같이 표시

### 자주 쓰는 명령어

| key                                      | function                                   | remark                                                                               |
| ---------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------ |
| `show [commit number]`                   | 원하는 커밋 내용 확인                               |                                                                                      |
| `difftool`                               | diff를 보기 편하게                               | • git difftool -w 브랜치1 브랜치2 --diff-filter=DM ./<br>(위처럼 필터 걸어서 원하는 diff만 출력해서 사용 가능) |
| `push -u origin master --force`          | 강제 push                                    |                                                                                      |
| `stash`<br>`stash list`<br>`stash apply` | 작업 내용 스택에 stach<br>stash 목록 확인<br>stash 적용 |                                                                                      |
