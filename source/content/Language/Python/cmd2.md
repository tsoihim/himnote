## CMD2란
CLI 시스템을 편하게 만들 수 있도록 다양한 기능을 제공하는 모듈
기존 시스템의 CLI를 대체하는 툴을 만드는 데에 본 모듈을 적용해보았음
우측과 같이 pip 통해 설치 `pip install cmd2`

## 사용방법
### Main Prompt
기본적으로 아래와 같이 main 프롬프트 클래스를 정의하고, 프롬프트의 cmdloop를 돌게하여 사용할 수 있다.

init을 통해 메인 프롬프트 클래스가 사용할 builtin 변수 지정 (intro/prompt 문구 등)
그외에 추가로 커스텀 변수를 추가하고 해당 변수가 수정될 때의 동작 또한 init에서 볼 수 있듯 지정 가능
또, 아래 코드처럼 bashrc와 같이 명령어 history도 파일에 저장하여 로드하는 것이 가능

### Output
시스템으로의 출력은 self.poutput(”[STR]”)을 사용
self.ppaged([STR], chop=[Boolean])을 사용하여 페이지에 맞게 curse처럼 출력도 가능

### Command
CLI에 대응하는 각 명령에 대해서는 아래와 같이 메소드를 추가할 수 있다
- do_[명령어 이름]
    - 명령어 실행 메소드
- complete_[명령어 이름]
    - 명령어 옵션 등 자동완성 위한 메소드
- help_[명령어 이름]
    - 명령어의 help 문구 출력 메소드
Cmd2에서 기본적으로 지원하는 명령어도 있으며, 아래와 같음
- set : 메인 프롬프트에 지정한 settable 변수를 변경
- alias : 특정 커맨드를 alias로 지정하여 사용
- macro : 특정 커맨드를 macro로 지정하여 사용 (alias와 다르게 입력 파라미터 지정 가능)
- history : 커맨드 히스토리 조회
- shell : 리눅스 명령어 실행해줌
```python
#!/usr/bin/env python
# coding=utf-8

import sys, cmd2,
import argparse

...
...

# MAIN PROMPT
class main_cmd(cmd2.Cmd):
    def __init__(self):
				super().__init__(
            persistent_history_file='~/.test_history', 
            startup_script='~/.trc', silent_startup_script=True
        )
				self.member = "test";
        self.add_settable(cmd2.Settable('member', str,  'Desc', onchange_cb=self.onchange_member))
				...

		def onchange_member(self, param_name, old, new):
        ...

    # cmd    
    def do_dump(self, inp):
        ...

    def complete_dump(self, text, cmd, start_idx, end_idx):
        ...

    def help_dump(self):        
        ...

if __name__ == '__main__':
    import sys
    prompt = main_cmd()
    sys.exit(prompt.cmdloop())
```

CLI 명령은 다양한 옵션을 수반하므로 do_[명령어 이름]과 같은 메소드에서 argparse를 사용하는 것도 좋음