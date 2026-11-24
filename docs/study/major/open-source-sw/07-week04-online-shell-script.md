---
title: "4주차 온라인 강의: Shell Script"
date: 2022-10-02
last_modified_at: 2022-10-19
category: major
tags: [오픈소스SW설계, Linux, Shell Script]
---

# 4주차 온라인 강의: Shell Script

작성일 2022-10-02 · 최종 수정일 2022-10-19
{ .post-date }

## `SHELL SCRIPT`

- `쉘 스크립트`

커맨드 라인 상의 명령어를 일괄적으로 처리하거나 반복적으로 수행하는데 아주 유용한 도구.  
리눅스나 유닉스 시스템에서는 시스템 관리에 필요한 기능들을 Shell Script로 만들어 둠으로써  
일괄적으로 처리할 수 있도록 도와준다.

- `쉘 스크립트`는 왜 쓰는가?  
    1. manual 하게 작업하면 error가 발생할 확률이 높지만, 자동화 시스템을 이용하여 에러 감소.  
    2. 단순한 일들을 묶어서 여러 일을 수행하는 Simple한 커맨드로 만들 수 있음.  
    3. 한 번 Script를 만들어 놓게 되면 다른 사용자와 공유할 수 있음.  
    4. 쉘 스크립트를 이용해서 어떠한 일(command)을 Interface로 만들어낼 수 있음.  
    5. 기존의 것들을 조합해서 새로운 명령어를 만들어 낼 수 있음.  
    6. (가장 큰 특징) `Compile을 하지 않음.`  `Bash Interpreter가 명령어를 해석`해서 수행.  
    → Interpretation 기반

- `Shell Script Interpreter`(쉘 스크립트 해석기)  
    → 해석을 담당함. 여러 종류가 존재한다.

    과거, 유닉스 초기에는 Shell Script가 /bin/csh 나 /bin/sh를 사용했지만, 최근에는  
    /bin/bash → bourne again shell 을 Interpreter로 사용하고 있다.

- `~$ echo ‘Hello’`
- `~$ echo ‘world’`

→ 이 두 개의 명령을 한꺼번에, 동시에 수행하려면 쉘 스크립트라는 것을 작성해야 한다.

- `~$ vi helloworld.sh` (nano 이용해도 됨.)

`#! /bin/bash` → 이 파일이 shell script라는 것을 지정해준다, `bash`에 의해 해석된다는 뜻.

`echo ‘hello’`

` echo ‘world’`  
→ 이와 같이 입력하고 저장.

- `ls -l helloworld.sh`  
    → 생성된 helloworld는 실행 불가. 파일의 권한이 -rw-rw-r— 이므로,  
        `chmod`를 통해 파일의 실행 권한을 추가해주어야 한다.
- `chmod +x helloworld.sh`
- `./helloworld.sh`

---

## `기본문법`

**`Basic Syntax`**

**`Basic`**

- 쉘스크립트는 Interpreter 기반으로 동작하며, Compile을 하지 않는다.

- 쉘 스크립트는 다른 개발 언어와 유사한 언어 문법을 갖는다.  
    1. 변수 (variables)  
    2. 함수 (functions)  
    3. 조건문 (if else…, case…)  
    4. 반복문(for, until, while…)  
    5. 주석(comments)  
    6. 디버깅

- Basic Syntax and Special Characters

| # | Used to add a comment, except #! → #!는 Bash에 의해 해석되는 쉘 스크립트 임을 알리는 기호이다. |
|---|---|
| \\ | Used at the end of a line to indicate continuation on to the next line → 다음 라인으로 줄바꿈할 때 사용한다. |
| ; | Used to interpret what follows as a new command → 여러 줄의 명령어를 수행할 때 사용한다 |
| \$ | Indicate what follows is a variable → 뒤에 오는 것이 변수명임을 나타낸다. |

`$make; make install; make clean`  
-&gt; 순차적 실행

`$make && make install && make clean`  
→ 앞에 오는 명령어가 올바르게 종료돼야 다음 명령 실행

`$cat file1 || cat file2 || cat file3`  
→ 앞에 오는 명령어가 정상적으로 종료되지 않으면 다음  실행

**`Functions`**

Declarations (선언) :

function_name () {     → argument를 명시적으로 지정하지 않는다.  
command…  
}

ex)

```shell
#! bin/bash

display() {
	echo "This is the message from the function"
	echo "the parameter passed from calling process is" $1
}

display "Bob"
display "Stuart"
display "James"
```

\#! /bin/bash를 지정해 bash에 의해 해석되는 shell script 임을 명시한다.  
function을 통해 넘어오는 argument는 명시하지 않고 사용할 때 넘어오면 사용한다.

**`Shell Commands`**

- `Built-in Shell Commands`

| Complied applications | Built-in bash commands | Other scripts |
|---|---|---|
| rm | cd |  |
| ls | pwd |  |
| df | echo |  |
| vi | read |  |
| gzip | logout |  |
|  | printf |  |
|  | let |  |
|  | ulimit |  |

- Complied applications는 /usr/bin or /bin에 실행파일이 저장되어 있는 명령어들이며,  
    Built-in bash commands는 Bash에 내장되어 있는 명령어들이다.
- Other scripts, 또 다른 Shell scripts를 호출해서 사용할 수도 있다.

- `Command Substitution`  
    명령어의 결과를 다른 명령어의 일부로 사용가능

    명령어를 backtick 마크 사이에 위치: ` … `  
    명령어를 \$( ) 사이에 위치

```shell
uname -r # 리눅스의 버전 정보 출력

pushd /lib/modules/`uname -r` # 디렉터리를 옮겨가는 cd와 유사한 명령어

---------------------------------

pushd /lib/modules/$(uname -r) # uname -r의 값으로 이동하게 된다...

이동한 후에 popd 누르면 빠져나온다.
```

Bash Command Interpreter가 해당 Shell scripts를 실행할 떄는, backTick 안에 넣은 명령어의 결과를 가져와서 스트링을 대체하게 된다.

**`Variables`**

- 변수 이름을 참조할 때는 \$ 기호를 변수 앞에 붙임
- 변수 값을 대입하거나 수정 시에는 \$ 기호를 사용하지 않음
- `HOME`, `PATH`, `HOST`와 같은 `표준 환경 변수` (environment variable) 일반 변수와 동일하게 사용됨(예: cd \$HOME)
- 환경 변수는 env, set, printenv 명령으로 얻을 수 있음

```shell
~$ echo $MYCOLOR

# 출력 X

~$ MYCOLOR=blue -> 변수에 값을 대입하거나 수정 시에는 $ 기호를 사용하지 않음
~$ echo $MYCOLOR

# blue

~$ echo $PATH

# 지정해 놓은 실행 파일들의 디렉터리 경로를 출력해준다.
```

**`Exporting Variables`**

- 스크립트 내에서 선언된 변수는 스크립트 안에서만 유효하다.
- 자식 프로세스 (Child process)가 변수를 접근하도록 하기 위해서는  
    export를 명시적으로 해야 함

```shell
export VAR=value # child process에서 VAR 사용 가능.

or

VAR=value; export VAR # 이 방법도 가능
```

**`Script Parameters`**

- 스크립트에 파라미터를 전달할 수 있다. 단, 이 방식으로 전달하는 파라미터는 함수에 전달되는 파라미터가 아니므로, 함수 내에서 파라미터를 참조하면 파라미터를 전달받을 수 없다. 함수에는 별개로 파라미터를 지정해서 함수에 전달해주어야 한다.

ex)

```shell
$ ./script.sh /tmp          # /tmp -> $1
$ ./script.sh 100 2000      # 100 -> $1, 2000 -> $2
```

| Parameter | Meaning |
|---|---|
| \$0 | Script name(awk와 유사) |
| \$1 | First parameter |
| \$2, \$3, … | Second, Third parameter, … |
| \$\* | All parameters |
| \$# | Number of parameters <br>→ Parameter들의 개수를 반환한다. |

```shell
nano ioscript.sh

----------------------

#! /bin/bash

echo -n "Enter your name:"
read sname     
# 입력을 읽어들여서 variable sname에 저장한다.

echo "Hello $sname. You are wonderful!"     
# 변수라는 의미로 $가 들어간다.
# 변수의 참조에는 $()를 사용하면 안된다.

----------------------

chmod +x ioscript.sh
ls -l ioscript.sh

# 실행 가능한 파일로 바뀐다.

----------------------

./ioscript.sh
홍길동

# Hello 홍길동. You are wonderful!
```

---

## `조건문`

**`if statement`**

- ConditionI(조건문)으로 가능한 것

    - 숫자 또는 스트링 비교
    - 명령어의 반환값 (0은 success)
    - 파일의 존재/접근 권한

```shell
if TEST-COMMANDS; then COMMANDS; fi

or

if condition
then
	statements

# elif and else are optional

elif condition
then
	statements
else
	statements
fi
```

**`Testing`**

- Example of testing files

```shell
if [ -f /etc/passwd ]; then
	Action
fi

# .sh에서 조건문을 쓸 때는 반드시 기호 사이에 띄어쓰기를 명시해주어야 한다.
```

- man 1 test 를 입력하면, if 문 안에 들어가는 조건문 확인 가능

| Condition | meaning |
|---|---|
| -e file | Check if the file exists |
| -d file | Check if the file is a directory |
| -f file | Check if the file is a regular file (i.e., not a symbolic link, directory, device node, etc.) |
| -s file | Check if the file is of non-zero size |
| -g file | Check if the file has sgid set |
| -u file | Check if the file has suid set |
| -r file | Check if the file is readable |
| -w file | Check if the file is writable |
| -x file | Check if the file is executable |

> 순서는 -x filename 이다.

- Example of testing of string

```shell
if [ string1 == string2 ]; then
	ACTION
fi

# != 를 이용해서 같지 않을 때 비교 가능
# .sh에서 조건문을 쓸 때는 반드시 기호 사이에 띄어쓰기를 명시해주어야 한다.
```

```shell
#! /bin/bash
# Section that reads the input

echo " Enter any color code [R OR Y OR G] : "

# echo와 echo -n의 차이점.
# echo는 한번 출력하면 자동으로 개행이 되지만,
# -n 옵션을 주면 개행이 되지 않는다.

read COLOR
echo $COLOR

# Section that compares the entry and display a message
if [ "$COLOR" == "R" ]
then
echo "STOP! LEAVE WAY FOR OTHERS"
elif [ "$COLOR" == "Y" ]
then
echo "GET READY YOUR WAY WILL BE OPEN SHORTLY"
elif [ "$COLOR" == "G" ]
then
echo "MOVE.. IT IS UR TURN TO GO"
else
echo "INCORRECT COLOR CODE"
fi
...

# 조건문에서 #COLOR를 " "로 묶어주는 이유는, 만약에 변수가 제대로 입력되지 않으면
# 실제 비교는 아무 것도 들어가지 않으므로, null과 character를 비교하게 돼서
# Syntax Error가 발생한다. 따라서, 변수가 제대로 입력되지 않았더라도 오류가
# 발생하지 않도록 " "로 감싸서 문자열로 만들어준 뒤, 그 안에서 변수를 참조한다.
```

- Numerical tests

    다음의 산술 연산자를 지원한다.

| Operator | Meaning |
|---|---|
| -eq | Equal to |
| -ne | Not equal to |
| -gt | Greater than |
| -lt | Less than |
| -ge | Greater than or equal to |
| -le | Less than or equal to |

**`Arithmetic Expression`**

- 다음의 세 가지 방법으로 산술 연산을 할 수 있다.

    1. expr 유틸리티를 사용  
        `echo $(expr 8 + 8)`

    2. \$((…))를 사용 (가장 자주 사용된다.)

        `echo $((x+1))`

    3. let을 사용

        `let x=(1 + 2) ; echo $x`

**`Example`**

```shell
#! /bin/bash

echo "please enter your age:"

read AGE

if [ $AGE -lt 20 ] || [ $AGE -ge 50 ] ; then
	echo "Sorry, you are out of the age range."
elif [ $AGE -ge 20 ] && [ $AGE -lt 30 ] ; then
	echo "You are in your 20s."
elif [ $AGE -ge 30 ] && [ $AGE -lt 40 ] ; then
	echo "You are in your 30s."
elif [ $AGE -ge 40 ] && [ $AGE -lt 50 ] ; then
	echo "You are in your 40s."
fi

----------------------

chmod +x agetest.sh
ls -l
# 실행권한 생성

./agetest.sh
```

---

## `반복문`

**`Looping constructs`**

- 작업을 반복되게 수행하도록 함

- Bash는 세 가지 반복문을 지원함

    - for
    - while
    - until

- `for`

```shell
for variable-name in list
# in list는 반복 범위
do
	...
	commands
	...
done

----------------------

#! bin/bash

sum=0
for i in 1 2 3 4
do
	sum=$(($sum+$i))
done
echo "The sum of $i number is: $sum"
```

- `while`

```shell
while condition is true
do
	...
	commands
	...
done

----------------------

#! /bin/bash
#
echo "Enter the number"
read no
fact=1
i=1

while [ $i -le $no ]
do
	fact=$(($fact * $i))
	i=$(($i + 1))
done
echo "The factorial of $no is $fact"
```

- `until loop`

```shell
until condition is false
do
	...
	commands
	...
done

----------------------

#! /bin/bash
#
echo "Number"
mn=1
mx=10
until [ $mn -gt $mx ]
do
	echo "$mn"
	mn=$(($mn + 2))
done
```
