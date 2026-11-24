---
title: "2주차: 디렉토리 구조, Editor, Pipe"
date: 2022-10-02
last_modified_at: 2022-10-13
category: major
tags: [오픈소스SW설계, Linux, 디렉토리 구조, Pipe]
---

# 2주차: 디렉토리 구조, Editor, Pipe

작성일 2022-10-02 · 최종 수정일 2022-10-13
{ .post-date }

Q. `시스템 콜`이란? ...

특정한 명령을 수행하기 위해 커널에 H/W 사용에 대한 요청을 보내는 것을 `시스템 콜`이라고 한다.

- `tree command`

    디렉토리 구조를 보기위해 tree 이용  
    =&gt; 안깔려 있기 때문에 sudo apt install tree 이용.  
    sudo apt update -&gt; sudo update command를 이용해 Ubuntu를 업데이트 가능.

```shell
cd /
tree
tree . -L 1
```

 -&gt; tree 구조를 상위 한 단계만 보여주는 것. 짧게 볼 수 있음.

---

## `디렉토리 구조`

`/bin` -&gt; 우리가 알 수 없는 0, 1. 실행 파일들이 주로 있는 영역. (이진 코드)  
/bin -&gt; usr/bin. /bin은 /usr/bin 에 대한  soft link, symbolic link이다. 바로가기로 이동하게 해준다.

`/boot` -&gt; 부팅 관련 파일들이 존재한다.

`/dev` -&gt; device...  
Unix에서는 디바이스에 파일로 접근할 수 있다.  
시리얼 포트로 데이터를 내보내거나 읽고 싶으면 tty를 이용하면 된다.  
그 디바이스 파일들은 /dev에 있다.

`/etc` -&gt; .conf(configure, 설정 파일들)  
=&gt; 실행을 위해 필요한 설정들의 값을 담아두는 파일들...  
우분투에서도 GUI 띄워서 수정할 수 있기는 하지만, 결국에는 etc에 들어있는  
파일들의 내용(수정 파일들의)을 변경하는 것이다.

`/hosts` -&gt; 주소들이 나온다.

ex) (IP 주소) (이름) 라고 지정하면,  
(이름)이라고만 지정하면 해당 주소에 접속 가능.  
이런 간편 설정과 관련된 사항들을 정의해놓은 곳이 /hosts

`/lib` -&gt; library, 시스템에서 사용되는 library file들이 들어있음.

`/lost+found` -&gt; 프로그램이 갑자기 꺼졌을 때 깨진 파일들이 보관되는 위치

`/media` -&gt; USB로 접속했을 때 파일들이 마운트 되는 위치

`/mnt` -&gt; disk를 다른 디렉토리 밑으로 갖다 붙일 때, mount할 때 쓰는 디렉토리.

`cd /mnt`  
윈도우즈의 드라이브... 나는 c, d, e, f까지 있음.  
mount는 외부 디스크를 리눅스의 파일 시스템에 갖다 붙여서 보이도록 해주는 작업으로 보인다.

윈도우즈에는 없고 리눅스에는 있는 프로그램들이 있다. 하지만 리눅스에서는 오픈 소스로 있는  
경우가 많은데,

ex) 논문 안에다가 이 그림을 넣고 싶다. (윈도우즈)  
해당 그림을 저장. 그런데 해당 그림에 여백이 많아서, 제거하고 싶다.  
리눅스에 pdfcrop이라는 오픈 소스가 있는데, (따로 설치해야함)  
아까 처럼 pdf에 여백이 많으면 여백을 잘라내고 그림만 남겨주는 utility이다.

아까 /tmp에 그 Windows에서 파일을 넣어놨는데,  
\~\$ pdfcrop /mnt/c/Temp/figures.pdf 를 실행하면,  
그림을 잘라서 수정해준다.

cf) !t   -&gt; 가장 최근에 입력한 t로 시작하는 명령어를 실행해준다.  
알파벳은 바꿔도 된다. (!tr 써도 됨. 즉, 구별할 수 있을 정도만 쳐주면 된다.)

`/proc` -&gt; System 전체에 대한 정보가 담겨있다.

```shell
cd /proc
cat cpuinfo
```

cpu에 대한 정보가 나온다.  
코어마다 가상의 하이퍼 쓰레딩 때문에 가상의 코어가 하나 더 있는 것으로 나와서  
더 있는 것처럼 보인다. 사실은 4개. (processor 7)

이 파일들은 리눅스 커널이 부팅하면서 시스템 정보를 갖다가 계속해서 보여주고 있는 것이다.  
`proc` 디렉터리 밑에 있는 파일들은 진짜 파일들이 아니라, 시스템 정보를 커널이 보여주기 위해서  
(cache...) 커널이 동적으로 만들어서 파일 내용을 보여주는 것이다. 그래서 시스템 상태가 바뀌면  
실시간으로 내용이 바뀐다.

`/sbin` -&gt; system binary, 리눅스 시스템에서 중요한 프로그램들. shutdown, reboot 등...

`/usr` -&gt; user 프로그램들이 깔리는 영역. Windows의 Program files 역할...

```shell
cd /usr
cd include
```

-&gt; cd `include`에는 프로그래밍에 필요한 `header file`들이 담겨있다.

ex) stdio.h stdlib.h string.h...  
/usr/include 에서 끌어다가 include해서 쓴다.

그런데, header 파일만 있으면 안되고, (이런 것들은 parameter가 뭐고 return 값이 뭐고 등을 알려줌)  
실제 구현은 `library` 폴더에 가야 있다.

lib으로 많이 시작한다... /usr/lib  
주로 많이 사용하는 라이브러리 들은 모두 여기 담겨있음!  
.so 파일들 -&gt; Windows의 DLL과 같음.

```shell
mv /mnt/c/Temp/figures-crop.pdf .
```

=&gt; Windows의 파일들을 옮겨올 수 있음.  
Macbook에서도 Linux 명령어를 대부분 다 사용할 수 있음.

cf) C++의 header file들은 h를 빼고 저장하는데, 어디에 있는가?

```shell
find . -name "iostream*"
cd c++/9/
```

`/var` -&gt; 시스템 상태를 기록하는 log 파일들이 주로 담기는 영역...

---

## `Editor`

vi  
nano, nano helloworld.txt

`cat` /usr/share/dict/words : 영어 사전. 틀리면 수정 제안을 제공해주는 것으로 보인다.

`less` /usr/share/dict/words : 넘겨가면서 볼 수 있음.  
/apple -&gt; apple이 들어간 단어들을 찾아준다.  
나갈려면 q

tail /usr/share/dict/words : 로그파일을 사용할 때 주로 사용한다.

tail -20 /usr/share/dict/words : 파일의 맨 마지막 20 줄을 출력한다.

tail -f /usr/share/dict/words : 파일 내용이 수정되는 것을 실시간으로 확인할 수 있다.  
=&gt; 나갈려면 Ctrl + C

---

## `io-redirection`

cat helloworld.txt를 하면 결과는 stdout으로 나온 것이다. (터미널로 나온 것)

cat helloworld.txt `>` goodbye.txt  
=&gt; goodbye.txt라는 파일이 생성되고, 그 내용은 helloworld.txt의 내용이 된다.

cat helloworld.txt `>>` goodbye.txt  
=&gt; goodbye.txt 파일 끝에 내용이 추가된다.

- wc : word counter  
    =&gt; stdin으로 입력을 받아서 그 내용의 줄이 몇 개인지, word는 몇 개인지, byte는 몇 개인지 알려줌.

wc  
Hello world  
good by  
good afternoon\~  
...  
Ctrl+D

3	6	36

wc &lt; helloworld.txt 를 하면, helloworld.txt의 내용을 stdin으로 wc에 집어넣어 달라는 뜻.

wc -l : 라인 개수만 본다.  
wc -w : 단어 개수만 본다.

io-redirection에는 방향성이 있다?

wc &lt; helloworld.txt 는 가능한데,

helloworld.txt &gt; wc 는 불가능하다…

> io-redirection은 항상 command [Operator &gt;, &gt;&gt; or &lt;, &lt;&lt;] file 로 동작한다.

---

## `Pipe` : 명령어를 연결해서 사용해주는 것.

`io-redirection`은 File에서 읽어오고 File에 넣어준다.

`Pipe`는 프로그램끼리 연결해주는 것.

ex) cat helloworld.txt | wc  
=&gt; Linux에서는 io-redirection이나 pipe를 많이 사용하는 것이 굉장히 중요하다.

Q. 파일이 몇 개 있는지 어떻게 구분하는가? (딕셔너리들도 사실은 다 파일이다.)

`ls | wc -l`  
파일 개수가 나온다.

Q. /usr/share/dict/words 안에 들어있는 단어의 개수는 몇 개인가?

cat /usr/share/dict/words | wc  
단어를 세어주는 프로그램을 별도로 짤 수도 있지만, File을 읽어주는 프로그램과  
단어의 수를 세주는 프로그램을 pipe 하여 하나의 새로운 프로그램으로 만들었다.

프로그램이 엉터리가 되는 과정은, 프로그램을 계속 갖다 붙여서 프로그램을 엉망으로 만드는 것...  
아주 간단한 일을 하는 작은 프로그램을 연결하고 조합하여 동작할 수 있도록 프로그램을  
설계하는 것이 유닉스의 원칙!
