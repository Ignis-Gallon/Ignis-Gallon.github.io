---
title: "12주차 (Assembly)"
date: 2023-06-02
last_modified_at: 2023-06-05
category: major
tags: [운영체제, Assembly, 분기, 서브루틴, 스택]
---

# 12주차 (Assembly)

작성일 2023-06-02 · 최종 수정일 2023-06-05
{ .post-date }

## Assembly

- JMP label : 무조건 분기 해주는 명령어.

    - Assembly는 한 줄 한 줄이 CPU 명령 하나라는 것이 가장 의미있는 것이다.
    - 명령어 한 라인 한 라인이 메모리에 그대로 저장되어 있고, 그걸 CPU burst로 가져가서 처리한다는 데서 의미가 있다.
    - 내가 직접 CPU가 처리하는 하나의 명령을 준다는 것이 의미있다.
    - Assembly는 복잡한 알고리즘은 처리하지 못하지만, Low level에서 처리할 수 있다.

내가 프로그램을 짜다가 코드를 보니 불필요한 부분이 많더라, 그 부분을 Loop를 돌리면 프로그램이 효율적이 될 것이라고 보이면, 분기(JMP) Label을 사용할 수 있다.

뒤에 조건을 만족하는 경우에만 해당 Label로 JMP하라는 것도 있었고, 만족하지 않으면 내려가게 되는 것도 있었다.

- 시스템 콜의 종류는 시스템이 제공하는 시스템 콜, 운영체제가 S/W적으로 제공하는 시스템 콜(trap)이 있다. 

    - MOV AH, 1
    - INT 21H
    - windows 하위 운영체제에서는 21H trap에서 subfunction 1번을 선택하는 것.
    - DOS 창에서 키보드로 값을 하나 입력받는 것(문자 하나)
    - 좀 더 찾아보면 입력 형태도 조절하는 것도 있다.

- 분기 명령어 (1)

```nasm
; 의사 명령
CODE SEGMENT
			ASSUME CS:CODE ; code segment 없음. 데이터 없이 코드만 있다.
			; intel 회사에서 CPU를 설계할 때 직접 DS 설정하는 명령어는 허용하지 않았음.
			; 왜냐면, 누군가가 실수로 혹은 의도적으로 세그먼트와 세그먼트를 바꿔버리는
			; 명령어가 존재할 수 있기 때문에 코드가 엉망이 될 수도 있기 때문이다.
			; 그래서 중요한 세그먼트는 직접 MOV가 불가능하고 Register를 거쳐서 가야 한다.

NEXT: MOV AH, 1
      INT 21H

			MOV DL, AL ; 위의 trap에서 입력받은 값은 AL에 자동으로 저장된다.
			INC DL ; INCREASE, DL = DL + 1을 의미.

			MOV AH, 2 ; DL register에 있는 값을 출력한다.
			INT 21H 	; 따라서 이전에 반드시 DL에 대한 처리가 있어야 함.

			JMP NEXT ; JMP, 조건이 없으므로 무한 루프를 돌린다는 뜻이다.

			; 운영체제에게 handle을 넘겨주는 trap
			; 명시적으로 쓰는 exit() 시스템 콜과 같은 것.
			; 
			MOV AH, 4CH
			INT 21H

			; 21H interrupt를 쓸 때, 어떨 때는 4CH을 쓰기도 하고,
			; 4C00H을 하기도 한다. 만약 00을 명시해서 준다는 것은
			; AL의 값이 중요하다는 것이고, 안주면 Not my business 라는 것
			; 이 프로그램을 종료하면서 던져주는 값이라고 생각할 수 있다.
			; 그러면 시스템은 그 메시지를 전달받아서 그 메시지를 저장하고 있다가,
			; 어떻게 종료되었는지를 체크해준다.

CODE ENDS
		 END
```

위의 프로그램은 무한 루프 프로그램이므로, 정상적으로 종료시키려면 조건을 만들어주어야 한다. 

1) 입력값이 X라면 종료

2) DL에 값을 넣기 전에 AL을 비교

3) CMP를 이용해 AL이 X이냐 비교

4) CMP AL, ‘X’

⇒ CMP 명령을 가보면, 같으면 return이 0이고 어느 쪽이 크냐에 따라서 1 또는 -1이 된다. 하지만 여기서는 같은지가 중요하므로 0인지가 중요하다.

5) JE (같으면 점프하라, zero flag를 0으로 setting하는 것) 이때 밑으로 점프시키면 된다.

```nasm
; 의사 명령
CODE SEGMENT
			ASSUME CS:CODE

NEXT: MOV AH, 1
      INT 21H

			CMP AL, 'x'
			JE HERE

			CMP AL, 'X'
			JE HERE
			; 소문자건 대문자건 x라면 JMP 시킴.

			MOV DL, AL 
			INC DL 

			MOV AH, 2 
			INT 21H

			JMP NEXT 

HERE: MOV AH, 4CH
			INT 21H

CODE ENDS
		 END
```

ex) Q. 대문자일 때는 + 1, Z는 증가 안시킴. 소문자는 a일 때 감소 안시킴.

⇒ ASCII 몰라도 ‘A’, ‘Z’ 사이냐, ‘a’, ‘z’ 사이냐 이런 것만 체크하면 됨.

---

- stack은 존재하며, 운영체제가 알아서 관리하고 운용한다.

```nasm
CODE SEGMENT
		 ASSUME CS:CODE

		MOV AX, 1234H
		MOV BX, 5678H

		PUSH AX ; AX의 값을 PUSH
		PUSH BX ; BX의 값을 PUSH
		PUSHF ; register로부터 Flag(두바이트)를 쌓는다.

		MOV AX, 2468H
		MOV BX, 2468H
		SUB AX, BX

		POPF ; 들어 있는 값을 끄집어 내서 flag register에 저장하라.
		POP BX
		POP AX

		MOV AH, 4CH
		INT 21H

CODE ENDS
		 END
```

- PUSHF는 현재 Flag 값(두 바이트)을 저장한다.
- PUSH AX
- PUSH BX
- POP AX
- POP BX

⇒ 이걸 하면 AX, BX의 값이 바뀐다. stack이기 때문. C언어는 지역 변수나 함수에서 함수로 인자를 넘겨줄 때 (보통 네 개까지는 레지스터를 쓰지만) 그걸 넘어가면 stack을 쓴다.

⇒ 프로세스 간의 메시지를 전달해주는 방법에 스택이나 큐를 쓰는 방법도 있었다.

---

- 서브루틴 사용법 (2)

ex) 0~9 숫자를 출력

```nasm
CODE SEGMENT
		 ASSUME CS:CODE
		 MOV CL, 0 ; 초기화

NEXT: CALL PUTNUM ; subroutine 호출, CL을 가지고 PUTNUM으로 JMP하라.
			INC CL ; CL = CL + 1
			CMP CL, 10 ; CL이 10보다 작은지 비교해서 JMP
			JB NEXT ; NEXT로 JUMP
			MOV AH, 4CH
			INT 21H

PUTNUM:
			MOV DL, CL
			ADD DL, '0' ; 문자 0에 숫자 1을 더함. DL은 문자 0이 된다.
			MOV AH, 2
			INT 21H ; 출력하는 인터럽트
			RET ; RET를 하면 JMP로 뛴 곳의 바로 다음으로 돌아감.
					; 즉, INC CL로 돌아가게 될 것. 마치 함수처럼 활용 가능.

CODE ENDS
		 END
```

Q. 소문자 a부터 z까지, 대문자 a부터 z까지 출력하고 싶다면 어떻게 해야 하는가? 이러면 CMP, 10이 52가 되어야 할 것. (26개, 26개)

중상 정도 : 거꾸로 출력하라. ⇒ 소문자 먼저, 나중에 대문자 등등… 

홀짝홀짝, 홀수만 출력하라 짝수만 출력하라?

모음은 출력하지 마라? 규칙이 없으면 조건을 달아야 함. (예외를 달아야 함.)

---

```nasm
.model small
.stack 100h
.data
	prompt db 'enter a lower case letter : $'
	msg    db 0Dh,0Ah, 'in upper case it is: '
	char   db ?, '$' ; 초기값 없이 빈 칸 생성.
.code
main proc

	; data segment를 쓰겠다, 그걸 ds에 알려주는 것.
	mov ax,@data ; get data segment, data가 있으면 이걸 쓴다.
	mov ds, ax
	
	; dx에 prompt의 시작 주소 offset을 전달.
	mov dx, offset prompt
	mov ah, 9
	int 21h ; display prompt

	mov ah, 1
	int 21h ; char in AL
	
	sub al, 20h ; converts to upper case
	mov char, al ; store char

	mov dx, offset msg ; msg라는 데이터의 offset을 가져온다.
	; 해당하는 데이터(msg)의 시작 주소를 얻을 수 있다.

	mov ah, 9 ; 9번 interrupt는 string을 출력하는 것.
	int 21h ; final message display
	; offset 주소에서부터 $가 나올 때까지 출력한다.

	mov ah, 4ch
	int 21h

main endp
end main
```

- string을 출력할 때, null의 기능을 하는 것이 \$이다. \$가 없으면 하염없이 나옴.
- 시작점은 dx가 가지고 있다. 주소로 가지고 있다. 이 프로그램에서 주소는 segment 주소 혹은 offset 주소가 있다. offset msg, offset prompt로 데이터의 시작 주소를 얻을 수 있다.
- 소문자 ‘a’와 대문자 ‘A’ 사이의 간격이 32. 16진수로 20H이다.
- 소문자와 대문자 사이는 일부러 20h만큼 떨어지도록 설계되었다.
- 따라서 al에서 20h(32)를 뺀다는 것은, 소문자를 대문자로 바꾼다는 것이다.

---

> Q. 난이도 하  
>   
> 1. 화면에서 문자 하나 입력 받아 그대로 다시 출력하기  
> 2. 소문자 하나 입력 받아 대문자로 출력하기(소문자만 입력)  
> 3. 2번 프로그램을 변형 (소문자가 아니면 무시하고 다시 입력 받기)  
>   
> Q. 난이도 중/상  
>   
> 1. 문자 하나 입력받아서 대/소문자 구분, 바꿔 출력하기  
> 2. 레지스터에 저장되어 있는 숫자를 2, 10, 16 진수로 출력해 보기.  
>      1자리, 2자리, 3자리  
> 3. 메모리에 저장된 숫자를 2, 10, 16 진수로 출력해 보기  
> 4. 구구단 프로그램 출력하기  
>      숫자 입력받아 출력하기  
>      메모리에 저장된 값으로 구구단 계산하여 출력하기, 여러단 출력하기  
>      계산한 구구단 값을 메모리에 저장하기  
> 5. 레지스터를 이용한 큰/작은 숫자 더하기. 그 값 출력하기  
> 6. 연속되는 문자를 입력받아 대문자/소문자 개수 구하기/변환하기  
> 7. 메모리에 저장된 문자가 대문자인지, 소문자인지, 숫자인지 구분하기.
