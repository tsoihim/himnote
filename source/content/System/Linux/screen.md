> **screen** - screen manager with VT100/ANSI terminal emulation
> 
> “Screen is a full-screen window manager that multiplexes a physical terminal between several processes (typically interactive shells).”

Screen은 Linux에서 백그라운드로 동작하는 가상 터미널을 띄워주는 윈도우 매니저.
이를 이용하면, 명령어 실행 후 터미널을 꺼도 명령어가 백그라운드에서 계속 돌아가도록 할 수 있다
(명령 실행 뒤 터미널 종료 → 나중에 screen 명령 통해 다시 접속하여 작업 이어가기)

### 보편적 사용법
```bash
$ screen -S [NAME] # screen에 이름 지정하며 진입

$ screen -R [NAME] # screen이 존재하면 진입, 아니라면 생성 후 진입

$ screen -ls # screen 리스트 표출

$ screen -x # 실행 중인 screen 진입

$ screen -S [NAME] -X quit # 스크린 삭제 (screen 내에서 창 모두 exit해도 삭제됨)

```

### Screen 내부 커맨드
- Ctrl+a, d : 현재 스크린으로부터 탈출(Deattach). (스크린은 꺼지지 않고 여전히 동작 중)
- Ctrl+a, c : 스크린에서 새창 띄우기
- Ctrl+a, 숫자 : 해당 번호의 스크린으로 이동
- Ctrl+a, n : 다음 창으로 이동 (Ctrl+a, space와 동일)
- Ctrl+a, p : 이전 창으로 이동 (Ctrl+a, Backspace와 동일)


**References**
- [https://bio-info.tistory.com/42](https://bio-info.tistory.com/42)