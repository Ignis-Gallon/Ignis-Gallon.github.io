---
title: "5주차: 실습 문제(Exit Code, Shell Script)"
date: 2022-09-29
last_modified_at: 2022-10-20
category: major
tags: [오픈소스SW설계, Linux, Shell Script, Exit Code]
---

# 5주차: 실습 문제(Exit Code, Shell Script)

- `Ken Thompson & Dennis Ritchie`  
    C의 창시자. Unix를 만드는데 Assembly로 만드는게 너무 힘들어서 C 개발.  
    → Write programs that do one thing and do it well.  
    → Write programs to work together

    프로그램을 연결하는 방법은 pipe, io-redirection 등을 이용하는 것이 있고,  
    조금 더 복잡한 프로그램의 결합은 shell script를 이용하면 된다.

```shell
ls
mkdir lab1-bash
cd lab1-bash/
ls
code .
```

- `EXIT Code`

```shell
int main() {
	return 0;
}

-> C언어 코드 작성할 때, 무의식적으로 이렇게 작성하는데, 어떤 의미인가?
-> return code는 OS에게 반환하는 것이다.
-> 0은 일반적으로 정상 종료를 의미하는 status code이며, 비정상적으로 종료됐다면
다른 status code를 return 하면 된다.

// test.c
int main() {
	return 10;
}

nano test.c
gcc test.c
./a.out
```

- `echo $?`

    방금 실행된 프로그램의 exit code를 보여준다.

ex)  
Q. `ls 를 실행했을 때, 정상 종료됐는가?`

```shell
ls
echo $?

# ls 명령의 exit code를 확인할 수 있음. => 0 즉, 정상종료
```

⇒ ls  
⇒ echo \$? 를 실행하면  
⇒ 0이 나와서, 정상 종료됐음을 알 수 있다.

Q. `ls hello.txt` (현재 경로에 없는 파일)

```shell
ls hello.txt # hello.txt가 현재 경로에 없다고 가정하자.
echo $?

# 출력으로 2가 나온다. 즉, ls가 정상 종료되지 않았다는 것을 알 수 있음.
```

Q. `./a.out && echo ‘hello world’` 를 하면, 어떤게 나올까?  
⇒ a.out이 정상 작동되지 않으므로, 아무 것도 나오지 않는다.  
⇒ &&는 앞의 명령이 정상 종료 되어야만 실행된다. (a.out은 위에서 작성한 test.c의 실행파일)

Q. `./a.out || echo ‘hello world’`  
⇒ || 는 true가 나올 때까지 실행하므로, hello world가 나온다.

Q. `./a.out ; echo ‘hello world’`  
⇒ ; 은 그냥 실행하도록 하는 것이므로, 성공 여부에 관계 없다.

- `Shell Script 실습`

    작은 명령어들을 모아서 프로그램을 작성하는 법 (더 복잡한 프로그램 작성)

```shell
#! /bin/bash

echo 'hello'
echo 'world'

# c file 저장 후 권한 변경, a+x 는 모든 유저에게 실행 권한 추가

chmod a+x myfirstscript.sh
# or
chmod +x myfirstcript.sh

# 실행
./myfirstscript.sh
```

- 쉘 스크립트는, 완벽한 언어는 아니다.  
    프로그래밍 언어와 비슷하게 쓰려면 변수나 반복문, 조건문 등이 필요하다.  
    (shell script comment : #)
- `변수`

```shell
#! /bin/bash

name = 'gildong hong'

echo "hello $name"
echo "good morning. $name~"

# echo 쓸 때 변수를 string에 포함하려면 쌍따옴표를 사용한다.
```

→ Shell script는 Interpreter 기반이기 떄문에 Compile이 필요 없다.

```shell
#! /bin/bash

echo -n "Your name: " # echo에서 -n 옵션을 사용하면 입력을 대기함.
read name # name 이라는 변수를 만들고, shell에서 입력 받는다.

echo "hello ${name}"
echo "good morning. ${name}~"

# String에서 변수 선언할 때 헷갈리면 Curly braces를 사용하기도 한다.
# 만약, 변수명 뒤에 ~를 붙이면 첫 글자를 소문자로 바꾸는 기능도 있다.
# echo "good morning. ${name~}"
```

- `함수`

```shell
#! /bin/bash

myfunc() { # 함수를 선언할 때, return이나 parameter를 명시하지 않아도 된다.
	echo 'hello world'
	echo 'good bye'

	echo $1 $2 $3 $4 # parameter 사용
	echo $5          # 아무것도 나오지 않음, why? 하단에 parameter가 4개 선언됨
	echo $*          # 모든 parameter 출력
	echo $#          # parameter의 개수 출력
}

myfunc # 함수 실행

myfunc hello world good bye 
# script(Interpreter)는 compile 과정이 없기 때문에, 엄격하게 체크안함.
# 함수를 통해 사용할 parameter를 이처럼 선언해서 사용하면 된다.
```

- `for loop`을 이용한 함수

```shell
#! /bin/bash

myfunc() {
	for name in $*; do     # 모든 parameter를 name 안에 집어넣어 준다. (참조)
	done
}

myfunc Kim Lee Park Choi Jung # 함수를 실행하고 arguments를 넘겨준다.

echo "Good bye~ $0 $1 $2 $3" 
# ./myshell.sh a b c ... => 터미널 상에서 변수를 받는 방법.
# $0는 실행한 파일의 이름을 출력한다.

# 실행 결과, 모든 이름이 for문 안에서 출력된다.
```

- `[실습] 명령어 대체, 산술 연산`

```shell
#! /bin/bash

myfiles() { # 내가 가진 file들을 쭉 찍어주는 함수를 작성한다.

	files = $(ls) 
	# ls 명령으로 directory의 list 읽어오기
	# 이와 같은 방식으로 shell script에서 shell command 사용 가능.
	# files = `ls` 이 방법으로도 커맨드 사용 가능하다.
	
	# $( ... ) 를 이용하면 ...의 실행 결과가 return 되며,
	# for loop과 같은 반복문 내에서 사용하면 결과를 하나씩 참조할 수 있다.

	for file in $files; do
		echo "Hello my file: $file"
	done
}

myfiles

mynumber=$((1+2+3)) # 숫자를 처리하는 방법, 산술 연산 하는 법
echo $mynumber # 산술 연산, 6 출력
```

- for loop을 원하는 count 만큼 돌리고 싶을 때가 있을 것이다.

    `seq` 를 활용한다.  
    man seq - print a sequence of numbers

`ex)` seq 10 → 1 … 10  
seq 5 10 → 5 … 10  
seq 0 2 10 → even numbers  
seq 1 2 10 → odd numbers

```shell
#! /bin/bash

myloop() {
	for i in $(seq 0 2 10); do 
# $() -> 이 안에 들어간 명령을 실행한 결과가 return 된다.
# $() -> 명령어 substitution
	...
	done
}

myloop # 실행
```

→ 아래모든 스크립트는 함수를 기반으로 기능을 구현한다.

- `[Q1]` 1부터 100까지 더하는 스크립트를 작성하자.
- `[Q2]` 파라미터로 전달받은 숫자들을 모두 더하는 프로그램을 작성하라.
- `[Q3]` 현재 디렉토리의 모든 텍스트 파일에 대해서 단어 개수를 출력하는 스크립트를 작성하라.  
    ex)  
    Hello.txt 8  
    Goodbye.txt 12

- `[Q4]` 현재 디렉토리의 실행 파일이 아닌 모든 파일의 단어 개수를 출력하도록 스크립트 작성.

**Q1**

```shell
#! /bin/bash

mysum() {

sum=0

        for num in $(seq 100); do # 1부터 100까지 $() 안에서 반환
                sum=$(($sum+$num))
                # sum=$((sum+num)) 
								# 위와 아래 둘다 사용 가능.
        done

        echo "${sum}"

}

mysum
```

**Q2**

```shell
#! /bin/bash

mysum() {

        sum=0

        for num in $*; do
                sum=$((sum+num))
#               sum=$(($sum+$num))
        done

        echo "${sum} or $sum"     # curly braces 사용하거나 안 해도 됨.

}

mysum 1 2 3 4
```

**Q3**

```shell
#! /bin/bash

word_counter() {

        files=$(ls *.txt)

        for file in $files; do
                echo "$file : $(cat $file | wc -w)"
        done

}

word_counter

# or

for file in $files; do
	wc=$(cat $file | wc -w)
	echo "$file : $wc"
done
```

**Q4**

```shell
#! /bin/bash

count_words_from_txt() {

        files=$(ls)

        for file in $files; do
                if [ ! -x $file ] ; then
                        echo "$file : $(cat $file | wc -w)"
                fi
        done

}

count_words_from_txt

# 우리가 예제를 진행하면서 .sh 파일들을 실행 파일로 바꿔놨기 때문에, .sh 파일은
# 카운트가 안된다. chmod -x file_name 으로 실행 권한을 뻇으면 카운트 된다!
```

- `Homework`  
    숫자를 랜덤하게 생성한다. (0\~100)  
    숫자의 범위를 입력받는다.  
    내가 숫자를 입력하면, 입력한 숫자가 랜덤 숫자보다 작거나 큰지를 보여준다.  
    → 랜덤 생성은 이렇게 하면 된다!

```c
~$ $RANDOM
-> RANDOM하게 값이 생성된다.
-> %를 이용하여 값의 범위를 지정한다.
```
