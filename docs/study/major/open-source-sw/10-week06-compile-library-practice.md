---
title: "6주차: 컴파일 과정, 라이브러리 실습"
date: 2022-10-06
last_modified_at: 2022-10-19
category: major
tags: [오픈소스SW설계, gcc, 컴파일, 라이브러리]
---

# 6주차: 컴파일 과정, 라이브러리 실습

작성일 2022-10-06 · 최종 수정일 2022-10-19
{ .post-date }

1. 컴파일 과정 이해하기
2. 여러 파일들로 나누어 컴파일 하기
3. 라이브러리에 대해 알아보기
4. 정적 라이브러리 만들어보기
5. 동적 라이브러리 만들어보기

## `컴파일 과정 이해하기`

```shell
mkdir lab2-gcc
cd lab2-gcc/

nano helloworld.c

# hello world를 출력하는 프로그램 작성
```

→ 프로그래밍할 때 보통 Tab은 쓰지 않는다. 사용자의 환경마다 Tab이 다르기 때문에, Indentation이 달라서, 보통은 Tab 보다는 보통 스페이스를 세 번이나 네 번을 쳐서 들어간다. (일반적)

1. `preprocessing`

```shell
gcc -E helloworld.c # stdout으로 preprocessing만 사용.
nano helloworld.i

# 그런데, 이렇게만 하면 stdout으로 나온다.
# redirection을 이용한다.
```

→ 이렇게 하고 nano helloworld.i를 해보면 이상한 내용이 추가되어있는데, 이 내용은 &lt;stdio.h&gt;에 있는 내용이 들어온 것이다. helloworld.i의 맨 아래로 가보면, 내가 작성한 코드가 정의되어 있다. (마지막에 조금 남아있음.)

2. `compile` (넓은 의미에서는 전체 과정을 다 compile이라고도 함)

```shell
gcc -S helloworld.i # compile만 하고 멈추는 옵션 -S
# helloworld.s 가 바로 생성된다.
```

nano helloworld.s를 해보면, 이 파일이 compile의 결과물이다.

- Windows(Intel)에서 Compile한 결과와 Macbook(ARM)에서 Compile한 결과는 Architecture가 다르기 때문에 .s 파일의 내용이 다를 수 있다.
- .s 파일을 보면, main: 이라고 되어있는 부분이 있는데, 그것이 우리가 작성한 main의 코드이다.

```nasm
main:
.LFB0:
	.cfistartproc
	endbr64
	pushq     %rbp
...
```

→ .LFB0는 주솟값으로 보인다. .s에서 main:의 내용은 printf를 호출하는 과정. 현재까지 Front-end에서 진행한 것이다.

3. `assembling` (assembly code는 사람이 이해할 수 있지만, 이제 실제로 기계만 이해할 수 있는 machine code, 즉, binary code로 바꾼다.)

```shell
as helloworld.s -o helloworld.o # assembling
chmod +x helloworld.o

./helloworld.o # 실행 불가
```

- od라는 프로그램을 이용해서 helloworld.o를 hexa로 정렬된 형태로 볼 수 있다. 그러나, 여기까지만 진행하면 실제로 프로그램이 실행되지 않는다.

<!-- -->

4. linking

```shell
gcc -o helloworld helloworld.o
```

- ls -l 을 해보면, 실행파일의 용량이 다른 것보다 굉장히 큰데, 이는 link가 됐기 때문에 그런 것이다. .i 파일의 용량이 큰 이유는 그냥 단순히 include 되는 걸 다 적어놨기 때문에 text size가 큰 것이다.

> 이제 ./helloworld 로 실행이 가능하다!

```shell
gcc hello.c -c
ls -l

# 위와 같이 해보면, link되기 전의 .o file 까지 생성해준다.
```

## `[실습]`

> foo(), goo() 함수를 각각 func1.c func2.c에 작성한다.  
> funcs.h는 foo()와 goo()의 선언을 갖는 헤더파일이다.  
> myapp.c에서 두 함수를 호출하도록 작성한다.  
> func2.c와 func2.c, myapp.c를 각각 func1.o, func2.o, myapp.o로 컴파일하고, 함께 링크하여 실행 파일인 myapp을 생성하라.

- ex) gcc -o myapp myapp.o func1.o func2.o  
    → 3개의 .o file을 link해서 myapp이라는 실행 파일을 만들어줄 수 있다.

> header file을 선언할 때, 이미 func1.c와 func2.c에서 #include &lt;stdio.h&gt;를 해서 필요한 기능을 다 include하도록 만들어 주었으므로, myapp.c에서는 #include &lt;stdio.h&gt;를 안해도 된다.

- func1.c와 func2.c에는 header file 선언을 안해줬는데도 error가 뜨지 않았다. 이는 type checking이 되지 않는 것으로, 문제가 있는 것이다.
- funcs.h가 두 번 들어가면 좋지 않으므로, 매크로를 추가로 사용한다.
- 그런데, 이와 같이 여러 개의 파일로 나눠두면, 굉장히 복잡하고 지저분해 보인다. 이렇게 하지말고, 구조를 만들어 보자. src, obj, include, bin Directory 들을 만들어서, 각각 .c, .o, .h, .exe 를 옮겨주자. (tree -L 2)
- 이렇게 분류한 파일들을 다시 컴파일 해보자

```shell
rm o/*
rm bin/*

gcc -c src/func1.c -o o/func1.o

# funcs.h : No such file or directory
# header file이 존재하지 않다고 error가 뜨므로, header file의 위치를 지정해주자.

gcc -c c/func1.c -o o/func1.o -I./include
gcc -c c/func2.c -o o/func2.o -I./include
gcc -c c/myapp.c -o o/myapp.o -I./include

# header file의 위치를 지정해주자.
```

- 이제 모든 .o file이 나왔으니, link 해주면 된다.

```shell
gcc -o bin/myapp o/*.o # -o를 지정해주지 않으면, default인 a.out이 나온다.

# or

gcc -o bin/myapp o/myapp.o o/func1.o o/func2.o
```

- myapp은 우리가 정의한 main이 있는데, func1과 func2는 다른 사람들도 쓰고 싶어한다고 하자. 그렇게 되면, 우리는 library directory를 만들어서 다른 사람들이 사용할 수 있도록 해줘야 한다.
- 이번에는 func1과 func2를 정적 라이브러리에 포함시킨 다음, myapp.o와 link해보자.

```shell
# lib directory를 새로 만든다. libfuncs.a를 담아둔다.

ar -rv lib/libfuncs.a o/func1.o o/func2.o
# ar command는 archive, archive는 static library를 의미

# 이렇게 하면, Static Library가 생성된다.

gcc -static -o bin/myapp o/myapp.o -L./lib -lfuncs 
# 여기서 순서가 바뀌면 안됨.
 
# 만약, 라이브러리 안에 무엇이 들었는지 확인해보려면 nm 커맨드를 이용하면 된다.
nm lib/libfuncs.a

func1.o:
                 U _GLOBAL_OFFSET_TABLE_
0000000000000000 T foo
                 U puts

func2.o:
                 U _GLOBAL_OFFSET_TABLE_
0000000000000000 T goo
                 U puts

T는 code를 의미하며, U는 undefined. 외부에 정의되어 있기 때문에 알 수 없다는 뜻.
```

- 그런데, ls -l을 해서 확인해보면, 둘 다 파일 사이즈가 똑같다.  
    ldd myapp.static을 확인해보면, myapp.static이 사용하는 동적 라이브러리가 무엇인지 출력해준다.

```shell
gcc -static -o bin/myapp.static o/myapp.o -L./lib -lfuncs

# 이렇게 하면, 파일 사이즈가 굉장히 많이 차이난다.
# -static을 지정해두면 static library로 linking 된다.
# 무엇이 좋은가?
# ldd myapp을 해보면, 사용되는 동적 라이브러리들이 나온다. 이것은 의존성이(.so) 있어서
# 만약 버전 정보가 달라지거나, 다른 곳에 가져가면 실행이 안된다.
# 그런데, ldd myapp.static을 해보면, not a dynamic executable 이라고 나온다.
# 따라서, 이 파일만 갖고 다니면, 어디서든 실행은 할 수 있다.
# c library는 경로가 지정되어 있는듯하다. 따로 -l로 지정안해줘도 된다.
```

> [심화실습] 공유라이브러리 .so를 만들어서 할 수 있다.  
> 공유 라이브러리는 좋은 점이, 다른 여러 프로그램에서 공유해서 사용할 수 있다.  
> ls /usr/lib에 가보면 공유 라이브러리가 있는데, ex) libmultipath.so. … 등이 있다.
>
> 프로그래밍할 때 사용하는 printf 등도 모두 ls /usr/lib에 정의되어 있다.  
> 이와 똑같은 것을 하는데, 공유 라이브러리를 만들어서 compile하면 된다.

- `[과제] 공유 라이브러리 만들어보기`

    정적 라이브러리에서 수행한 것과 동일한 것을 동적 라이브러리로 만들어본다.  
    ldd를 이용해 동적 라이브러리의 의존관계를 파악한다.

    \*실행을 위해서  
    export LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:라이브러리 패스를 수행해야함.
