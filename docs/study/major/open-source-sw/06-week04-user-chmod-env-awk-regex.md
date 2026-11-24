---
title: "4주차: 유저 추가, chmod, 환경 변수, awk, 정규표현식"
date: 2022-09-24
last_modified_at: 2022-10-14
category: major
tags: [오픈소스SW설계, Linux, chmod, awk, 정규표현식]
---

# 4주차: 유저 추가, chmod, 환경 변수, awk, 정규표현식

현재 일반적으로 사용되는 터미널은 터미널 애뮬레이터.  
멀티유저 환경으로 사용되는 일이 최근에는 줄어들었지만,  
`유닉스(리눅스, 우분투)`는 기본적으로 `여러 명의 유저`가  
사용하도록 만들어진 시스템이다.

---

## `User Environment`

- `whoami` -&gt; 자신의 아이디가 나온다.  
    대부분 윈도우 디폴트와 같은 이름으로 설정되어 있다.

실제로 이 시스템에 어떤 유저가 있는지 모든 유저를 확인하려면,  
홈 디렉터리 밑에 있는 /etc 디렉터리를 가본다. (/etc/passwd 에는 모든 유저에 대한 정보가 있음)

```shell
cd /etc
tail passwd or cat passwd
vi passwd or vi group
```

명령어를 입력하면 유저 관련 파일 이동, 파일 확인 가능  
(유저는 현재 사용하고 있는 쉘 확인 가능, :로 구분되며 7번째 record, /bash, born again shell)

`우분투`는 가상의 유저를 만들어서 접근 권한을 제어한다던지 등의 기능을 제공한다.  
(아파치 웹서버 등 서비스에 대한 권한이 그 예로 보인다.)

단, 보안 때문에 /etc/passwd에서 user의 passwd는 제공되지 않는다. 'x'로 표시됨.

`그룹 확인` -&gt;

`sudo` 그룹은 굉장히 중요한 그룹이다.  
다시 passwd 파일을 보면, root 유저가 있는데, root 유저는 다른 유저의 파일을 모두 들여다 보고 무엇이든 할 수 있다. 그런데 리눅스 에서는 해커에 대한 위협도 막고, 본인이 루트가 되었을 때 시스템을 잘못해서 날릴 수도 있기 때문에 로그인 자체를 막아버린다.  
-&gt; root 상태에서 날리면 복구 불가능! 그래서 그냥 접근 자체를 막아버림.

그래서 `sudo `그룹에 속해있는 유저들에게만 `sudo command`를 이용해서 (그 command를 실행시키는 순간에만) 잠시 root user권한을 얻도록 해준다.

## `유저 추가`

```shell
sudo adduser joshua

cd /etc
cat passwd
# joshua에 대한 정보가 출력된다.
```

- 일반적으로 우분투에서 외부 컴퓨터로 로그인하려면,  
    `ssh joshua@<IP 주소>`
- 자기 자신에게 로그인하려면,  
    `ssh user@localhost`

wsl은 port가 방화벽에 막혀있어서, 로그인이 불가능.  
`ssh: connect to host localhost port 22: Connection refused`

- `su - joshua` -&gt; joshua로 로그인 가능.

wsl에서 만든 계정은 wsl에서만 사용되며, 윈도우 계정과는 전혀 관련이 없다.

다른 계정으로 로그인한 터미널에서, cd /home -&gt; 다른 계정 cd. 다른 사람의 계정으로 로그인해서 들어가서 파일 확인과 파일 수정 등, 권한에 맞는 활동을 할 수 있다... (멀티유저가 있다면)

=&gt; 이를 방지하기 위해 내가 보호하고자 하는 파일의 `접근권한`을 바꿔준다 `(chmod)`

ls -l -&gt; 접근권한, 소유자 이름, 소유 그룹 이름 등 출력

owner(user), group, others

## `chmod`

`chmod o-r mysleep.c`  
=&gt; 이렇게 하면 joshua는 파일을 읽을 수 없다.

그런데, 다른 유저가 내 디렉터리에 들어오는 것 자체를 막을 수도 있다.

ls -al을 썼을 때 첫 번째에 나오는 디렉터리는 현재 디렉터리 인데, 디렉터리에서의 x권한은 디렉터리 접근 권한이다. 따라서 others에서 x권한을 뺏으면 된다.

`chmod o-x .` =&gt; 현재 경로를 들어오지 못하게 막는다. 이처럼, 그냥 내 home 디렉터리에 들어오지 못하게 막으면 된다.

`Q. others가 내 홈 디렉터리 들어오지 못하게 하는 방법.`

```shell
~$ chmod o-x .

# or

chmod o-x $HOME # 환경변수

# or

chmod o-x /home/user

# or

~$ chmod o-x /home/user
# 해커들이 루트 권한을 뚫고 들어올려고 엄청나게 노력을 한다.
```

## `환경 변수`

- 쉘에서 쓰는 프로그램들이 사용하는 변수.

ex) `$HOME` =&gt; home directory  
`echo $HOME`  
→ 그 사용자에 맞춰서 사용자의 home directory가 나오게 된다.  
→ 환경 변수는 절대경로로 사용하지 않는다.

- `env` : 모든 환경 변수를 보는 command
- `$PATH` : 우리가 실행해주는 명령어의 디렉터리 위치를 저장해 두었기 때문에, 커맨드를 실행할 수 있다. 그런 경로를 저장해두는 환경 변수.

명령어들은 대부분 /usr/local/bin /usr/local/sbin 등에 들어있어서, shell에서 추가적인 입력 없이 명령어 입력만으로 사용할 수 있게 된다.

- mysleep.c를 PATH에 등록하기.

    mysleep.c가 있는 디렉터리로 가서 `export PATH=$PATH:$HOME/경로`  
    \$PATH: 를 쳐줘야 기존 환경변수에 새로운 경로를 추가하겠다는 뜻.  
    띄어쓰기 쓰면 안됨!!!

mysleep

=&gt; 환경변수를 활용해서 경로를 적용하고 이와 같이 mysleep이 없는 경로에서 ./mysleep 을 실행하더라도, \$PATH 환경 변수에 저장되어 있기 때문에 잘 실행되는 것을 확인할 수 있다.

## `Text Matching`

**`Text Matching(awk)`**

프로그램을 짤 때 스트링을 짜거나 텍스트를 수정하면서 스트링을 만들어내는 것은 매우 중요하다.

Q. /etc/passwd에서 user라는 pattern이 들어간 line만 보여달라  
cat /etc/passwd | grep user

/etc/passwd를 보면, :으로 연결되거나, space로 연결되어서  
패턴, 필드 단위로 나타나는 것을 조작하는 커맨드 =&gt; awk

## `awk`

`awk '{print $0}'` =&gt; 모든 필드를 다 출력하는 것으로 보인다.

`awk -F: '{print $1}' /etc/passwd` =&gt; :로 쪼갰을 때, 첫 번째 필드를 출력하라.

유저의 홈 디렉터리만 뽑는 것, 유저 번호까지 뽑는 것은, \$3을 추가해준다.

`awk -F: '{print $1 "\t" $3 "\t" $6}' /etc/passwd`

=&gt; 정렬하려면? | sort 이용. 즉,  
`awk -F: '{print $1 "\t" $3 "\t" $6}' /etc/passwd | sort`

sort를 거꾸로?  
`awk -F: '{print $1 "\t" $3 "\t" $6}' /etc/passwd | sort -r`

- --&gt; 지금 까지의 sort는 첫 번째 column을 기준으로 했고,  
    2라고 지정하면 두 번째 column을 기준으로 sort한다. (숫자)

`렉시코그래픽오더? (사전 순으로 sort하는 것.)`

Q. 사전 순으로 sort되기 때문에, column 2를 기준으로 sort하면,  
추가적인 옵션 지정이 필요하다.

`awk -F: '{print $1 "\t" $3 "\t" $6}' /etc/passwd | sort -gk 2`

`awk -F: '{print $1 "\t" $3 "\t" $6}' /etc/passwd | sort -nk 2`

고급 유저가 되면, 자동화를 하기 위해 커맨드 툴을 사용한다.

## `정규표현식(Regular Expression)`

**`Regular Expression`**

=&gt; grep에서 이용? 온갖 곳에서 이용된다...

> 정규 표현식이 사용될 수 있는 부분
>
> sed : text file에 존재하는 특정 pattern을 다른 string으로 replace하기  
> awk : -F: 로 seperator를 지정하고 seperator를 기준으로 특정 field 출력하기  
> tee : stdout으로 들어온 string을 stdin으로 file에 전해주고, 다시 stdout으로 출력  
> find : 특정 경로내에서 파일이 존재하는지 찾기  
> grep : 특정 파일 내에서 특정 패턴을 포함하는 라인이 존재하는지 찾기

Q. /usr/share/dict/american-english 에서 단어 찾기.  
\*z로 시작하는 단어 찾기.

cat /usr/share/dict/american-english | grep ^z  
=&gt; 단어의 개수?  
cat /usr/share/dict/american-english | grep ^z | wc -w

- ly로 끝나는 단어 찾기.  
    grep ly\$
- o .. o =&gt; o와 o 사이에 n개의 단어가 들어가도록 grep

o로 시작, o로 끝나며 가운데에 1개 이상의 글자가 들어가는 문자열.

- " "를 붙여서 패턴이라고 표현해줄 수 있음. 다른 커맨드가 아니라!

Q. o와 o 사이에 적어도 한 개 이상의 글자가 들어가는 단어를 찾으시오.

A. .\*를 하면, 공백도 일종의 스트링으로 취급해서 공백이 나올 수도 있다. 따라서 다음은 안된다.

o.\*o

그래서, 올바르게 하려면 적어도 한 개 이상의 문자가 존재하도록 해야하므로

o..\*o

를 해야 맞다.
