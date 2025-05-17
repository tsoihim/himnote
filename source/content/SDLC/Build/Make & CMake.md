## **CMake**
Build process를 작성한 CMakeLists.txt에 작성된 내용을 바탕으로 각 환경에 맞는 Makefile을 생성해 주는 툴
- 단순히 Makefile을 생성해 주는 것이기 때문에 Make를 통해 실제 빌드 명령어들을 수행해야함

## **Make**
make는 파일 관리 유틸리티로, 반복적인 명령 자동화를 할 수 있도록 함 (꼭 빌드가 아니어도 수행가능)
Makefile이 있는 디렉토리에서 make를 입력하여 컴파일하는 등의 자동화 가능
파일 간의 종속관계를 파악하여 Makefile에 적힌 대로 명령 순차적으로 실행

## Build System
![[Pasted image 20250517151608.png|500]]

## **no build tool vs. make vs. cmake 시나리오**
1. autogen.py main.c hello.c run.sh 만 존재
2. autogen.py로 autogen.c를 생성
3. main.c hello.c autogen.c 를 compile 해서 obj 파일을 생성
4. main.o hello.o autogen.o 와 library m 을 링크해서 app 이라는 파일을 생성함.
5. 생성된 app과 run.sh 을 /home/ 경로에 복사
### no build tool
```bash
$ python autogen.py
$ gcc -c main.c hello.c autogen.c
$ gcc -o app main.o hello.o autogen.o -lm
$ cp app /home/
$ cp run.sh /home/
```
### make
```makefile
all : app run.sh
	cp app /home/
	cp run.sh /home/

app: main.o hello.o autogen.o
	gcc -o app main.o hello.o autogen.o

main.o: main.c
	gcc -c main.c

hello.o: hello.c
	gcc -c hello.c

autogen.o: autogen.c
	gcc -c autogen.c

autogen.c : autogen.py
	python autogen.py
```

```makefile
install_dir=/home/
SRCS = $(wildcard *.c)
OBJS = $(SRCS:%.c=%.o)
LIB = -lm

all : app  run.sh
	cp app $(install_dir)
	cp run.sh $(install_dir)
app : $(OBJS)
	$(LD) -o $@ $^ $(LIB)

$(OBJS) : %.o : $(SRCS) autogen.c
	$(CC) -o $@ -c $<

autogen.c : autogen.py
	python autogen.py

```
### cmake
```
project(app C)

file(GLOB SRC *.c)
execute_process(COMMAND python autogen.py)

add_executable(app ${SRC} ${AUTOGEN})
find_library(m math)
target_link_library(app ${m})

install(CODE “execute_process(COMMAND cp run.sh /home”)
install(CODE “execute_process(COMMAND cp app /home”)
```

## 자주 쓰는 CMake Syntax
### commands
- pre-build
    - set(${variable} ${value})
        - 변수 세팅
    - execute_process(COMMAND ${command})
        - sh 에서 수행 하듯이 command 를 수행 하는 방법 보통 이부분에 script 를 넣음.
    - add_subdirectory(${subdirectory})
        - ${subdirectory}에 CMakeLists.txt 가 존재해야되며. build를 좀 더 나눠서 하기 위해서 subdirectory를 둠
- build
    - add_custom_target(${target})
        - Makefile 의 ${target} : ${dep} 에서 ${target} 같은 역할. “make ${target}” 하면 dep 하위 까지 다 build 됨.
        - 보통 target 을 잡고 add_custom_command 를 넣어서 실제 명령을 수행.
    - add_custom_command(TARGET ${target} \[options] COMMAND ${command} … )
        - custom target 에 대한 실질적인 build. Makefile의 ${target} : ${dep} 에서 ${dep} 같은 역할.
    - include_directories(...)
        - cflags 의 -I 옵션
    - add_executable (${EXE} ${DEP})
        - 최종 파일을 생성할 때 executable 형태로 생성
    - add_library(${LIB} \[SHARED] ${DEP})
        - 최종 파일을 생성할 때 library 형태로 생성
    - target_link_library(${target} ${dep})
        - executable or library 를 만들 때 필요한 다른 library들을 link 하는 명령
- package
    - install(CODE “${cmake command}”)
        - build 가 끝나고 make install 시 수행되는 절차
        - 보통 ${cmake command} execute_process 명령을 통해 script 로 복사
### Variables
- directory
    - CMAKE_SOURCE_DIR
        - CMakeLists. 파일이 있는 root directory
    - CMAKE_BINARY_DIR
        - cmake 를 실행 한 cwd
    - CMAKE_CURRENT 로 시작할 경우. 하위 subdirectory 의 CMake root 경로를 지칭함.
- compiler
    - CMAKE_C_FLAGS
        - compile flags
    - CMAKE_SHARED_LIBRARY_LINKER_FLAGS
        - link 시 사용할 flags
    - CMAKE_COMPILER
        - compile 시 사용할 compiler (clang, gcc, …)
    - CMAKE_C_STANDARD
        - compile 표준 버전. CMAKE_C_STANDARD_REQUIRED true 랑 보통 같이 쓰임


**References**
- https://gist.github.com/dongbum/d1d49e38a20f9cf52ea39f9ce2702160
- http://ebook.pldworld.com/_eBook/make/make_utility_lecture_\(cWyANG\).pdf
- https://www.gnu.org/software/make/manual/make.html#Overview
- https://velog.io/@mtak0235/Makefile