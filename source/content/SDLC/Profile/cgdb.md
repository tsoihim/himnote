<aside>  cgdb : 디버깅 툴. gdb에 curse 사용하여 편하게 디버깅할 수 있도록 하는 툴

</aside>

### Usage
- Process Attach
    - cgdb -p \[PID]
- Core File with Executable
    - cgdb \[CORE FILE PATH] \[BINARY PATH]
        - 소스코드와 함께 분석이 가능

### Cmds

| key            | function                            |
| -------------- | ----------------------------------- |
| info threads   | 스레드 모두 출력                           |
| info locals    | 현재 스택의 로컬변수 모두 출력                   |
| info variables | 전역변수 모두 출력                          |
| p [변수명]        | 해당 변수 출력, 포인터 변수에 *써서 가리키는 변수 출력 가능 |
| p *[포인터]       | 구조체 배열일 때 배열 크기 출력                  |
| bt             | 전체 스택 프레임 출력(up, down으로 이동 가능)      |
