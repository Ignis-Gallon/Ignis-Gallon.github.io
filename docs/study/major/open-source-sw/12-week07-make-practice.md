---
title: "7주차: Make 실습"
date: 2022-10-13
last_modified_at: 2022-11-03
category: major
tags: [오픈소스SW설계, Makefile, make]
---

# 7주차: Make 실습

## `Make`

kernel.org 에서 latest version linux kernel을 다운로드 받는다.

```shell
mkdir lab.make

wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.0.1.tar.xz # 복사된 주소

# 설치가 끝나면, 압축을 해제한다.

tar xvf filename
```

- 이 linux file 안에 cfile이 몇 개인지 세보자.

```shell
find . -name "*.c" | wc -l
# c 소스코드 파일 개수 찾기

find . -name "*.h" | wc -l
# .h file 개수 찾기

find . -name "*.S" | wc -l
# assembly file 개수 찾기
```

- assembly code는 사람이 짠 코드이다. arch 등의 directory에 들어가보면 assembly가 많다. arch directory에 들어가보면 디렉터리들이 많은 이유가, architecture의 종류가 굉장히 많기 때문이다.(사람이 짠 assembly는 .S)  
    → 추가로, -E는 preprocess 과정까지 진행 (.i 생성) -S는 compile 까지만 진행 (.s 생성) -c는 assembler 까지만 진행 (.o 생성) 옵션을 추가로 지정하지 않으면 link까지 진행해서 컴파일
- C를 사용하려면 Stack과 Heap영역이 초기화가 되어있어야 하는데, 부팅 전에는 초기화가 안되어있다. 그래서, 부팅 전 단계에서는 C를 사용할 수 없으므로 assembly로 작성해준다.
- 이 많은 소스 코드들을 compile하기 위해서 필요한 것이 무엇인가? → 컴파일을 자동화시켜 줄 필요가 있다. compile을 자동화 시켜주는 규칙이 필요한데, 이를 makefile로 만들어준다.

```shell
# makefile의 개수 찾기
find . -name "[Mm]akefile" | wc -l
```

- 상위 디렉터리로부터 recursive하게 compile해가면서 결과적으로 우리가 사용하는 아주 작은 kernel이 나오게 되는 것이다. 노트북 쓰는 사람은 노트북에 kernel을 깔아서 configuration을 설정할 수도 있다. 즉, 커널을 바꿀 수 있다. compile하는데 생각보다 시간이 엄청 걸린다.
- Make 실습을 위해서 이전 실습에서 만든 .c 파일과 .o 파일, .exe 파일을 생성한다.
- 소스 코드를 수정할 때마다 컴파일을 다시 진행하기 힘드므로, make를 통해 규칙을 다시 만들어준다.

```shell
make # 이와 같이 입력하면, 현재 디렉터리에 있는 Makefile을 찾아서 컴파일을 진행한다.
```

```shell
          
 ./bin/myapp: ./obj/func1.o ./obj/func2.o ./obj/myapp.o
				gcc -o ./bin/myapp ./obj/func1.o ./obj/func2.o ./obj/myapp.o

./obj/func1.o: ./src/func1.c
        gcc -o ./obj/func1.o -c ./src/func1.c -I./include

./obj/func2.o: ./src/func1.c
        gcc -o ./obj/func2.o -c ./src/funcs.c -I./include

./obj/myapp.o: ./src/myapp.c
        gcc -o ./obj/myapp.o -c ./src/myapp.c -I./include

# .h file은 dependency를 따로 지정해주지 않는 이유가, make에서 header는 자동으로 지정해주는
# 과정이 있다.
```

- 만들어놓은 object file을 다 지우고 compile해보자. (.o file이 모두 생성된다.)
- myapp.c에 foo(); goo();를 두 줄 추가해보자. 이 경우에도 새로 make 진행.
- linux kernel files 중에서 파일이 수정되면, 어떤 것을 수정하고 어떤 것을 바꿔야되는지를 찾기는 굉장히 어렵다. 그렇기 때문에 Makefile로 규칙을 만들어두면 의존성을 따라가면서 compile하면 되기 때문에, 굉장히 빠르게 수정된다. (모든 파일을 compile할 필요가 없다.)

> 이제 make를 개선해보자!

```shell
# phony target

all: ./bin/myapp # 최상위 target을 명시하기 위해서 주로 사용한다.

clean:
	rm -f ./obj/*.o
	rm -f ./bin/myapp
	# clean에서는 원하는 파일을 모두 지울 수 있는 것으로 보인다.

# ----- #

~$ make all # source code가 바뀐 것이 없기 때문에 아무런 동작 X
~$ make clean # 싹다 지운다. clean을 만들지 않아도 되지만, 대부분의 make는 갖고 있다.
```

```shell
# suffix 규칙

./bin/myapp: ./obj/func1.o ./obj/func2.o ./obj/myapp.o
        gcc -o ./bin/myapp ./obj/func1.o ./obj/func2.o ./obj/myapp.o

./obj/%.o: ./src/%.c
        gcc -o $@ -c $< -I./include

clean:
        rm -f ./obj/*.o
        rm -f ./bin/myapp

# ----- #

make # 똑같은 기능으로 compile 된다.

# ----- #

touch src/func1.c # 날짜 최신화

make # func1.c만 새롭게 compile
```

```shell
PROJ_DIR = $(PWD)
INC_DIR = $(PROJ_DIR)/include
SRC_DIR = $(PROJ_DIR)/src
OBJ_DIR = $(PROJ_DIR)/obj
BIN_DIR = $(PROJ_DIR)/bin

all: $(BIN_DIR)/myapp

$(BIN_DIR)/myapp: $(OBJ_DIR)/func1.o $(OBJ_DIR)/func2.o $(OBJ_DIR)/myapp.o
        gcc -o $(BIN_DIR)/myapp $(OBJ_DIR)/func1.o $(OBJ_DIR)/func2.o $(OBJ_DIR)/myapp.o

$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
        gcc -o $@ -c $< -I$(INC_DIR)

clean:
        rm -f $(OBJ_DIR)/*.o
        rm -f $(BIN_DIR)/myapp

# MACRO를 사용하는 이유는 무엇인가?
# 추후에 경로이름을 수정하거나, 경로를 변경할 때 한꺼번에 변경해주기 위해서이다.

# ----- #

# 만약 src directory name -> source 로 바뀐다면, 위에만 정의를 변경해주면
# 나머지는 바꾸지 않아도 된다.
```

- funcs.h에 hoo라는 함수를 추가하고, func3.c를 만들어보자!
- OBJS에 \$(OBJ_DIR)/func3.o를 추가해주면, make가 원활하게 동작한다.

```shell
PROJ_DIR = $(PWD)
INC_DIR = $(PROJ_DIR)/include
SRC_DIR = $(PROJ_DIR)/src
OBJ_DIR = $(PROJ_DIR)/obj
BIN_DIR = $(PROJ_DIR)/bin

OBJS = $(OBJ_DIR)/func1.o $(OBJ_DIR)/func2.o $(OBJ_DIR)/func3.o $(OBJ_DIR)/myapp.o

all: $(BIN_DIR)/myapp

$(BIN_DIR)/myapp: $(OBJS)
        gcc -o $(BIN_DIR)/myapp $(OBJS)

$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
        gcc -o $@ -c $< -I$(INC_DIR)

clean:
        rm -f $(OBJ_DIR)/*.o
        rm -f $(BIN_DIR)/myapp
```

> header file은 따로 makefile에 적어주지 않고, 따로 적어줌으로써 자동으로 생성되게 한다.

```shell
# DEP = $(OBJS:$.o=$.d)
# 이 macro를 만들어주고,
# -MMD 옵션을 추가해주자.

GNU nano 4.8                                            Makefile                                            Modified  PROJ_DIR = $(PWD)
INC_DIR = $(PROJ_DIR)/include
SRC_DIR = $(PROJ_DIR)/src
OBJ_DIR = $(PROJ_DIR)/obj
BIN_DIR = $(PROJ_DIR)/bin

OBJS = $(OBJ_DIR)/func1.o $(OBJ_DIR)/func2.o $(OBJ_DIR)/func3.o $(OBJ_DIR)/myapp.o
DEP = $(OBJS:$.o=$.d)

all: $(BIN_DIR)/myapp

$(BIN_DIR)/myapp: $(OBJS)
        gcc -o $(BIN_DIR)/myapp $(OBJS)

$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
        gcc -o $@ -c $< -I$(INC_DIR) -MMD

clean:
        rm -f $(OBJ_DIR)/*.o
        rm -f $(BIN_DIR)/myapp
```

- 이후, 만들어진 .d file을 확인해보면 그 내용이 다음과 같다.

```shell
/home/user/lab.make/obj/func1.o: /home/user/lab.make/src/func1.c \
 /home/user/lab.make/include/funcs.h

# .o file이 .c, .h에 의존한다는 것을 의미한다. 이것들이 자동으로 생성되는데,
# 이 규칙들을 Makefile에 넣어주면된다.

GNU nano 4.8                                            Makefile                                                      PROJ_DIR = $(PWD)
INC_DIR = $(PROJ_DIR)/include
SRC_DIR = $(PROJ_DIR)/src
OBJ_DIR = $(PROJ_DIR)/obj
BIN_DIR = $(PROJ_DIR)/bin

OBJS = $(OBJ_DIR)/func1.o $(OBJ_DIR)/func2.o $(OBJ_DIR)/func3.o $(OBJ_DIR)/myapp.o
DEP = $(OBJS:%.o=%.d)

all: $(BIN_DIR)/myapp

$(BIN_DIR)/myapp: $(OBJS)
        gcc -o $(BIN_DIR)/myapp $(OBJS)

$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
        gcc -o $@ -c $< -I$(INC_DIR) -MMD

-include $(DEP) 
# 이 라인을 추가하면, gcc preprocessor가 어떤 .h file이 생성한지를 체크한다.
# -MMD 옵션이 필요한 header를 파악해서 .d file로 생성해달라는 의미이다.
# header file을 생성하는 규칙은 -include $(DEP)에 모두 들어가게 된다.

clean:
# gcc 같은 명령어를 추가해줘도 된다.
# distclean은 중간에 생성된 모든 것을 지운다. directory도 지운다.
        rm -f $(OBJ_DIR)/*.o
        rm -f $(BIN_DIR)/myapp
```

> 이후, header file을 살짝만 고쳐주고나서 다시 make를 실행하면 모두 새로 컴파일된다.  
> why? 모든 file들이 header file에 dependency를 가지고 있기 때문이다.

- make를 수행하면, 최종 목표인 all: \$(BIN_DIR)/myapp을 수행하기 위해서 매번 각각 다른, 필요한 동작들만 실행하는 것으로 이해하면 된다.
