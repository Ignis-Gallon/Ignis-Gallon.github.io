---
title: "Assembly-8: 두 자리 숫자 출력, 레지스터 보호"
date: 2023-05-18
last_modified_at: 2023-06-05
category: major
tags: [운영체제, Assembly, 숫자 출력, 스택]
---

# Assembly-8: 두 자리 숫자 출력, 레지스터 보호

작성일 2023-05-18 · 최종 수정일 2023-06-05
{ .post-date }

- 한 자리 숫자를 출력하는 것은 쉽다. 하지만 두 자리 숫자를 출력하려면? 나누고, 쪼개야 한다.
- 36을 출력하고 싶다면? ‘3’, ‘6’을 출력해야 한다.
- 그리고 이걸 16진수, 2진수, 10진수로 출력하는 것들. 이런 것들이 있을 수 있다.

---

17 (10)

11 (16)

0001 0001 (2)

10진수로 17을 출력할 수 있어야 하고, 이진수로 0001 0001도 출력할 수 있어야 한다.

---

```nasm
title Display

dosseg
.model   small
.stack   100h

.code
main    proc
        ; 6CH 출력. 0b 0110 1100
        mov     al, 6CH
        mov     cx, 8
        
L1:
        shl     al, 1 ; al을 왼쪽으로 한 번 shift 시키라.
        mov     dl, '0'
        ; 한 번 적용하면, 110 1100 0 이 될 것이다.
        ; 떨어져 나간 값은 어디로 갈까? 보통 Carry Flag로 넘긴다.
        ; 한 바이트를 넘어가는 것은 거의 bit 하나밖에 없기 때문에, 
        ; 거의 carry flag를 사용한다.

        ; 이전에 ADD가 있었고, ADC가 있었는데,
        ; ADD는 0000
        ;       1111
        ;       ----
        ;       1111
        ; 이 되는데, ADC는

        ;       0000 1111
        ;       1111 F351
        ; 이런 덧셈을 할 때 사용된다.
        
        ; SHL을 사용할 때, 넘어서는 bit로 인해 Carry Flag가 사용된다.
        jnc     L2 ; 앞에서 연산을 했는데, carry가 발생하지 않는다면 jump.
                   ; carry가 발생했으면 넘어간 것이 1이라는 뜻.
        mov     dl, '1'

L2:
				push ax
        mov     ah, 2
        int     21h
				pop ax        

        loop    L1

				mov     ax. 4c00h
				int     21h

main    endp ; end procedure
        end     main
```

⇒ 이렇게 하면 좋지 않은 점. al과 ah는 붙어있기 때문에 바이트로 구분되어 있지만, 영향을 받지 않는다고 생각할 수는 없다. 그래서 ah, al을 동시에 사용한다면 보호할 필요가 있다. 그래서 내가 al을 쓰고있을 때는 al의 값을 바꾸지 않는다고 선언해주어야 한다. (보호해 준다.) 가장 간단한 방법은 stack을 이용해서 ax를 보호하는 것이다.

```nasm
title Display

dosseg
.model   small
.stack   100h

.code
main    proc
        ; 6CH 출력. 0b 0110 1100
        mov     al, 6CH
        mov     cs, 8
        
L1:
        shl     al, 1 ; al을 왼쪽으로 한 번 shift 시키라.
        mov     dl, '0'
        ; 한 번 적용하면, 110 1100 0 이 될 것이다.
        ; 떨어져 나간 값은 어디로 갈까? 보통 Carry Flag로 넘긴다.
        ; 한 바이트를 넘어가는 것은 거의 bit 하나밖에 없기 때문에, 
        ; 거의 carry flag를 사용한다.

        ; 이전에 ADD가 있었고, ADC가 있었는데,
        ; ADD는 0000
        ;       1111
        ;       ----
        ;       1111
        ; 이 되는데, ADC는

        ;       0000 1111
        ;       1111 F351
        ; 이런 덧셈을 할 때 사용된다.
        
        ; SHL을 사용할 때, 넘어서는 bit로 인해 Carry Flag가 사용된다.
        jnc     L2 ; 앞에서 연산을 했는데, carry가 발생하지 않는다면 jump.
                   ; carry가 발생했으면 넘어간 것이 1이라는 뜻.
        mov     dl, '1'

L2:
        push    ax
        mov     ah, 2
        int     21h
        pop     ax

        loop    L1

				mov     ax. 4c00h
				int     21h

main    endp ; end procedure
        end     main
```

이와 같이 ax의 기존 값을 보호할 수 있게 된다. 
