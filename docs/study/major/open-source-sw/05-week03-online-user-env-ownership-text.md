---
title: "3주차 온라인 강의: 사용자 환경, 파일 소유권, 텍스트 처리"
date: 2022-10-02
last_modified_at: 2022-10-20
category: major
tags: [오픈소스SW설계, Linux, 환경 변수, chmod, sed]
---

# 3주차 온라인 강의: 사용자 환경, 파일 소유권, 텍스트 처리

## `User environments`

- Linux is a `multi-user operating system`

    - meaning more than one user can log on at the same time.
    - To identify the current user, type `whoami`
    - To list currently logged-on users, type `who`

- `Users and Groups`

    - 모든 User는 ID를 갖는다. `(UID)`
    - Linux는 User를 분류하기 위해 Group을 사용하며,  Group 또한 ID를 갖는다. `(GID)`
    - User는 최소 한 개 이상의 Group에 소속된다.
    - User 관련 file : `/etc/passwd`  
        Group 관련 file : `/etc/group`

```shell
tail -5 /etc/passwd
tail -5 /etc/group
```

- `Adding and Removing Users`

    - `useradd` (or `adduser` for Debian)

        ```shell
        sudo useradd joshua
        ```

    - `userdel` (or `deluser` for Debian)

        ```shell
        sudo userdel -r joshua
        ```

        → -r : recursive, home directory에 있는 내용까지 다 지우는 옵션.

    - `groupadd` (or `addgroup`)

        ```shell
        sudo groupadd anewgroup
        ```

    - `groupdel` (or `delgroup`)

        ```shell
        sudo groupdel anewgroup
        ```

        → group 추가 시, /etc/group file에 생성된 group이 추가된다.

- `root` account and `sudo` command

    - `root account`는 시스템 전체에 대해서 접근 권한을 가진 계정이다.
    - 그렇기 때문에, 유저의 실수와 해킹의 위협에 대비하기 위하여 Ubuntu에서는 root account에 대한 로그인 권한을 차단해놓았다.
    - 대신 `root account`의 권한을 잠시 빌려올 수 있는 command인 `sudo`를 사용할 수 있다.

- `Environment Variables(환경 변수)`

    사용자별로 다른 환경 변수를 설정하면, 사용자가 정의한 환경 변수에 따라 다른 동작을 한다.

    - 환경 변수는, application에 사용될 수 있는 `Character String`이다.
    - 환경 변수는 유저가 생성하고 값을 설정할 수 있으며, command shell과 다른 application에 사용할 수 있다.

    ```shell
    env | head -10
    ```

    → 환경 변수를 보는 방법, head로 맨 처음 10줄만 출력한다.

- `Setting Environment Variables`

    ```shell
    echo $PATH
    # 환경 변수를 출력하는 방법
    ```

    ```shell
    export VARIABLE=value

    #or

    VARIABLE=value; export VARIABLE
    ```

    ```shell
    #1 editor 들어가서 쓰라는 내용.
    ~/.bashrc
    VARIABLE=value

    #2 내용을 쉘에서 바꾸고 적용시킬 때 사용하는 커맨드
    source ~/.bashrc #or source를 더 많이 쓴다!
    .~/.bashrc

    nano ~/.bashrc 들어가서
    VARIABLE=value 추가한 뒤,
    shell에서 source ~/.bashrc 하면 적용됨.
    이후 echo $VARIABLE 하면 정상적으로 출력가능.
    ```

    → 로그인 할 때마다 변수의 값이 자동으로 설정되도록 하고싶을 때

- `.bashrc`

    `~/.bashrc` file is invoked every time a new command line shell (or terminal window) is created

    \~/.bashrc file defines `aliases` and `environment variables`

## `File Ownership`

- Linux에서는 모든 것을 File로 취급하며, Device에 접근할 때도 File 취급한다.
- Linux에서는 모든 파일이 user(owner)와 group에 연관되어 있으며, 소유자와 접근권한이 명시되어 있다. read, write and execute.
- `chown` : Used to change `user ownership` of a file or directory
- `chgrp` : Used to change `group ownership`
- `chmod` : Used to `change the permission` on the file.  
    Permission can be set separately for `owner`, `group` and `other`

- `chmod`

    ```shell
    chmod uo+x, g-w myscript.sh

    chmod 755 somefile
    # user : 111, group : 101, other : 101
    # User, Group, Other에 대한 r, w, x 권한을 8진수로 설정한다.
    ```

- `chgrp`

    ```shell
    chgrp joshua myDir # 이와 같은 방법으로 그룹 소유자 변경 가능
    ```

## `Manipulating Text`

```shell
# goodbye.txt

# I'm Groot!
# I'm Groot!
```

- `sed` : Stream Editor; sed is used to modify the contents of a file or input stream  
    → file이나 stdin으로 들어오는 text를 수정한다. (매칭되는 string을 다른 string으로 고친다.)

```shell

sed s/pattern/replace_string/ file
# Substitute first string occurrence in every line
# 단, 수정한다고 해서 원본 파일의 내용을 바꾸는 것은 아니다.
# 바꾼 내용을 저장하려면, ioredirection이나 pipe를 이용한다.

# -------------------------------------------------------

# ex)
sed s/"I'm"/"You're"/ goodbye.txt

#
# You're Groot!
# You're Groot!

# -------------------------------------------------------

# example code
sed s/"I'm"/"You're"/ goodbye.txt > newgoodbye.txt

# You're Groot!
# You're Groot!
# 위의 내용을 갖는 newgoodbye.txt file이 생성된다.

sed s/"I'm"/"You're"/ goodbye.txt >> newgoodbye.txt

# You're Groot!
# You're Groot!
# You're Groot!
# You're Groot!
# 위의 내용처럼 두 문장이 newgoodbye.txt에 추가된다.

# -------------------------------------------------------

sed s/pattern/replace_string/g file
# Substitute all string occurrences in every line

# ex)
sed s/'o'/"xx"/g newgoodbye.txt

# Yxxu're Grxxxxt!
# Yxxu're Grxxxxt!
# Yxxu're Grxxxxt!
# Yxxu're Grxxxxt!

# -------------------------------------------------------

sed 1,3s/pattern/replace_string/g file
# Substitute all string occurrences in a range of lines

# ex)
sed 1,3s/'o'/"xx"/g newgoodbye.txt

# 1~3번 줄의 o만 xx로 치환됨.

# -------------------------------------------------------

sed -i s/pattern/replace_string/g file
# Save changes for string substitution in the same file
# 바뀐 내용을 저장한다.
# -i option을 사용하면, filename을 명시해줘야 한다.
# 따라서, pipe를 이용해 stdin을 받아들여 sed를 이용하려면
# -i를 사용할수 없다. 왜냐면, pipe를 사용하면
# filename을 지정하는 방식이 아니기 때문이다.
# 따라서, io-redirection을 사용해야 한다.

# 여기서 문제
# cat groot.txt | sed 2,3s/"You're"/"I'm"/g > groot.txt 등으로 바꾸면
# 내용이 날라감...
```

> 위에서 안 된다고 한 경우의 예시는 다음과 같다.  
> wow.txt  
> hey! hey! hey!  
> hey! hey! hey!  
> hey! hey! hey!
>
> cat wow.txt | sed s/”hey!”/”first hey!”/g &gt; wow.txt
>
> 자기 자신에 대해서 저장하는 명령어는 동작하지 않는 것으로 보인다.  
> 왜냐면, 자기 자신에 대해서 하는 것은  
> sed -i s/”hey!”/”first hey!”/g wow.txt  
> 로 이미 가능함.

- `awk` : awk extracts and prints specific contents of a file and often used to construct reports  
    → 특정 record에 있는 내용을 출력한다.

```shell

awk '{ print $0 }' /etc/passwd
# Print entire file

# -------------------------------------------------------

awk -F: '{ print $1 }' /etc/passwd
# -Fx 를 이용하면, x에  seperator를 지정하여 record 별로 나눌 수 있음.
# Print first field (column) of every line, separated by a space.
# /etc/passwd 파일을 awk로 출력하면, 이름을 얻게 됨.
# line은 \n으로 구분되어 있음. 실제로는 space로 구분.

# -------------------------------------------------------

awk -F: '{ print $1 $7 }' /etc/passwd
# Print first and seventh field of every line

# -------------------------------------------------------

# example)

awk -F: '{ print "name : "$1 "\tshell: " $7 }' /etc/passwd | head -5
# 위와 같이 seperator로 구분된 특정 record를 문자열과 함께 출력할 수 있음.
```

- `sort` : sort is used to rearrange the lines of a text file (주의! text 정렬임. 숫자는 옵션 필요)

```shell

sort filename
# line을 정렬하며, line의 첫 character를 기준으로 정렬

# -------------------------------------------------------

cat file1 file2 | sort
# 두 개의 파일 내용을 동시에 출력하고, pipe를 통해 함께 정렬함.

# -------------------------------------------------------

sort -r filename
# 역순으로 정렬함

# -------------------------------------------------------

sort -k 3 filename
# 각 라인의 세 번째 필드를 기준으로 정렬함.

sort -t':' -k 3 /etc/passwd | tail -5
# -t'x' 옵션을 통해 seperator를 지정할 수 있음.

sort -t':' -nk 3 /etc/passwd
# -n 옵션을 통해 string을 number로 바꿔서 숫자로 정렬할 수 있음.
```

- `grep` : Scan files for specific patterns; regular expression can be used.

```shell
# 1)

grep [pattern] filename
# Search for a pattern in a file and print all matching lines
# pattern의 검색에 정규 표현식을 사용할 수 있음.

# -------------------------------------------------------

# 2)

grep -v [pattern] filename
# Print all lines that do not match the pattern

# -------------------------------------------------------

# 3)

grep [0-9] filename
# Print the lines that contain the numbers 0 through 9

# example

grep [0-9] /etc/passwd
# /etc/passwd 에서, 0, 1, 2, ... ,9 까지의 숫자 중 하나라도 갖고 있으면 출력됨.

# -------------------------------------------------------

# 4)

grep -c 3 [pattern] filename
# Print context of lines (specified number of lines above and below the pattern)
# for matching the pattern. Here, the number of lines is specified as 3

# 바뀜. -c 옵션은 해당 패턴이 포함된 줄의 count를 stdout으로 내보냄.

# Tip : [...] 를 이용하면, [ ] 안에 들어가는 것 중 한 character라도 포함하면 출력됨.
# 숫자는 물론, character도 가능함.
```

- `tee` : tee takes output from any command, and send it to both a file and to stdout.  
    → stdout을 stdin으로 받아 file로 저장하고, 다시 stdout으로 내보내준다.

```shell
find . -name "*.txt" | tee textfiles.txt
# *.txt file들을 찾아서 그 내용을 textfiles.txt에 저장한 뒤, stdout으로 내보낸다.
```

- `wc` : word counter
- `Regular Expressions(정규 표현식)`

The quick brown fox jumped over the lazy dog

| pattern | Usage | example |  |
|---|---|---|---|
| . | Match any single character | a.. | Matches azy |
| a \| z | Match a or z | b. \| j. | Matches both br and ju |
| \$ | Match end of string | ..\$ | Matches og |
| ^ | Match beginning of string | l.\*<br>l.\*y | Matches lazy dog<br>Matches lazy |
| \* | Match preceding item 0<br>or more times | The.\* | Matches the whole<br>sentence |

or 조건은 사용법이 바뀜.

```shell
grep -E "pattern1|patter2" filename

# 그러나, 아래 코드와도 기능이 같다고 볼 수 있음.

grep [pattern1pattern2] filename # 단, 알파벳 또는 숫자로 한정
```
