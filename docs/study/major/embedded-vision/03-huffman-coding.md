---
title: "5주차 (Chpt. 3 : Huffman-Coding)"
date: 2023-05-25
last_modified_at: 2025-05-24
category: major
tags: [임베디드비젼시스템, 데이터 압축, Huffman Coding, Shannon-Fano Coding, Golomb Code]
---

# 5주차 (Chpt. 3 : Huffman-Coding)

## Shannon-Fano Coding

Shannon’s theory를 이용한 첫 번째 theory이다.

- 알고리즘

    - Empty Codes를 만든다.
    - 각 symbol의 빈도를 계산한다.
    - 빈도에 따라 symbol을 정렬한다.
    - 그룹A, 그룹B를 나누고, 그것의 차분을 가장 적게 만들어준다.
    - 첫 번째 그룹에 0을 부여하고, 두 번째 그룹에 1을 부여한다.
    - 이를 더 이상 자를 수 없을 때까지 반복한다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/01.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/02.webp)

a : 00

b : 01

c : 100

d : 101

e : 110

f : 111

- Shannon-Fano Coding은 항상 optimal한 prefix를 부여하지는 않는다. 왜냐면, 빈도수가 낮은 것에 짧은 bit 수를 부여하는 것이 더 좋은데, Shannon-Fano Coding은 항상 그렇지는 않기 때문이다. 이걸 개선하는 것이 이후에 나온 `Huffman-Coding` 이다. 

- Huffman-Coding의 결과가 Entropy와 같아지는 경우를 Optimal하다고 하는데, 이는 모든 Symbol의 발생 확률이 1&#47;2\^n 일 때만 가능하다.

    - 예컨대 Symbol이 네 개라고 한다면, 각각의 Symbol의 발생 확률이 모두 1&#47;4이다.

---

## Huffman-Coding

Huffman-Coding은 먼저 Sorting을 해야 한다. 각각의 Symbol에 Probability를 할당하고, 오름차순으로 정렬한다. 이때, 가장 빈도수가 낮은 두 개에 1과 0 (이 교재에서는 앞선 것에 1을 할당)을 할당하고 두 개를 합쳐서 새로운 Symbol로 만들어준다. 이때, 확률은 두 개의 Symbol의 확률을 더한 것이다. 이 작업이 끝났을 때 확률 정렬 순서가 변하게 된다면, 다시 Sorting 해준다. 

이후에 만약 Probability가 같은 것이 여러 개가 있다면 어떤 것을 묶어도 문제가 없다.

이렇게 Huffman-Coding을 진행하면, 빈도수가 높은 것에 적은 bit code가 할당된다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/03.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/04.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/05.webp)

`Average code length` (code rate) l : 0.4\*1 + 0.2\*2 + 0.2\*3 + 0.1\*4 + 0.1\*4 = 2.2 bits/symbol

`Entropy` : -0.2log0.2 -0.4log0.4 -0.2log0.2 -0.1log0.1 -0.1log0.1 을 하면, 2.122 bits/symbol

`Redundancy` : l - H = 0.078 bits/symbol

- example

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/06.webp)

---

## Min Variance Huffman Trees

- 모든 버젼의 average code length는 동일하지만, variance는 다르다.
- 가장 좋은 Huffman Coding은 variance가 최대한 작아야 한다.

---

## Extended Huffman Codes

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/07.webp)

- Entropy : -0.8log0.8 -0.02log0.02 -0.18log0.18 = 0.816 bits/symbol

- Huffman Coding을 진행하면 위의 예시와 같이 나오게 할 수도 있다.

    - l 을 구하면, 1.2 bits/symbol이 된다.  
        ⇒ 0.8\*1 + 0.02\*2 + 0.18\*2 = 1.2
    - Redundancy는 1.2 - 0.816 = 0.384 bits/symbol (47%)  
        ⇒ 이걸 더 좋게 해볼 수 있을까?

- 두 개의 Letter를 하나로 묶는 것으로 Extended Huffman Coding을 고려할 수 있다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/08.webp)

이와 같이 나올 수 있는 모든 sequences of two letters를 묶어서 나올 수 있는 확률을 곱한 table을 만들고, 이것에 대한 Huffman-Coding을 실시한다. 그리고 이것의 length를 구하게 되면 1.77228이 나오게 되고, 두 개의 letter를 묶어서 code를 만들었으므로, 2를 나눠줘야 하나의 letter에 대한 length가 나오게 된다. 따라서 length는 0.8614이다.

Redundancy = l - H = 0.8614 - 0.816 = 0.0045 bits/symbol

> 두 개의 letter를 묶어서 계산했을 때 Redundancy가 많이 줄어들었으므로, 여러 개의 letter를 묶으면 더욱 효율이 좋아질 것이다. 그런데, 만약 ASCII code를 세 개의 letter로 묶어주게 된다면 너무 많은 양의 Table이 만들어지게 되고, 빈도가 0인 codewords도 존재하기 때문에, 실용성은 떨어지게 될 수도 있다. 

---

## Adaptive Huffman-Coding

Huffman-Coding을 진행하기 위해서는 빈도를 분석하고, 이를 바탕으로 encoding을 진행했다. 그런데, 이 방법은 실시간 처리에는 적용되기 어렵다. 이를 해결하기 위해 Adaptive Huffman-Coding이 사용된다.

- Procedure

    - Encoding
    - Decoding

- Encoding

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/09.webp)

- NYT는 Not Yet Transmitted를 의미하며, 0은 들어온 Symbol의 개수, 51은 앞으로 들어올 Symbol의 개수를 의미한다.
- 아래의 테이블은 기본적으로 주어지는 Table이다. 예컨대, a를 표현하는 방법은 두 가지가 있을 수 있는데, 첫 번째로 아무런 정보가 없을 때 a는 아래의 테이블을 통해서 구해지게 된다. 두 번째로 a라는 것이 들어온 다음에 테이블에 a라는 것이 존재한다면, 아래와 관계 없이 Output Table을 통해서 Coding을 하게 된다.  
    ⇒ 즉, 처음에 값이 들어올 때만 기본 Table을 사용하고, 이후에 다시 들어올 때는 Output Table에서 정해진 값을 사용해 Coding하게 된다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/10.webp)

- NYT는 항상 0이고, 처음에 a가 들어오게 되면 NYT는 0, a는 1을 부여한다.
- 그리고, 처음에 들어올 때는 초기 Table의 값을 사용하므로, Output은 우선 00000이 된다.
- 들어올 Node의 개수는 Table의 Cell의 개수가 n이라고 할 때 2n-1이다.
- a가 들어오면서 a의 codeword는 1이 되었고, NYT는 0을 할당받았다.
- 새로운 문자가 들어올 때마다 노드가 추가되므로, 상위 노드가 하나 추가되었다. 현재 들어온 것이 하나 뿐이므로, 1을 더해준다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/11.webp)

- a가 다시 들어오게 되면, 전체 들어온 개수는 두 개이므로 개수를 하나 추가해주고, 이미 Output Table에 정보가 있으므로 해당 정보를 가져온다.
- 따라서, Output은 00001이 된다.
- a 노드의 개수도 2가 된다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/12.webp)

- 다음에 r이 들어오게 되면, NYT가 아래로 밀리게 되고, NYT는 00이 되며, r은 01이 된다. a는 그대로 1이다. 
- 이때, NYT는 0이고 r은 10001이므로, Output에는 0/10001이 추가된다.
- 들어온 symbol이 세 개이므로 최상위 노드는 3이 되고, 새롭게 추가된 노드는 처음 노드를 받게 된 것이므로 1이 된다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/13.webp)

- 이번에는 d가 들어왔다. 새로운 것이 들어왔으므로 Output에는 현재 NYT의 값이었던 00이 추가되고, d의 할당값인 00011이 추가된다.
- 새로운 노드가 추가되고, 이전 노드와 현재 노드에 모두 1을 더해준다.
- NYT는 아래로 밀리게 되므로, 000이 되고, d는 001이 된다.

> 여기까지 보면 규칙이 생기게 되는데, 분기를 담당하는 노드를 기준으로, 각각의 하위 노드들의 값은 왼쪽이 오른쪽보다 클 수 없다.  
>   
> 무슨 말이냐면, 현재 값이 (4)인 최상위 노드 아래에는 하위 노드 (2) (2)가 존재한다. 만약 다음에 새로운 Symbol이 들어오게 되면, 좌측의 하위 노드의 값이 (3)이 될 것이고, 이러면 규칙이 붕괴된다. 이럴 경우, 좌|우를 뒤집어준다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/14.webp)

- v가 들어오면서 NYT가 분화되었다.
- Output도 추가되었다. NYT 000이 추가되었음. (아직 v는 추가되지 않았으나) v는 10101이기 때문에 Output에 추가되어야 한다. 그런데 문제가, v가 추가가 되어버리면, 이전에 말했던 문제가 발생하게 된다.
- 이 경우, Tree의 아래서부터 타고 올라가면서 문제가 되는 부분을 교체한다.
- 현재 문제가 되는 부분은 총 두 곳이 있음.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/15.webp)

- 우선 문제가 되는 첫 부분을 교체했고, (3) | (2)가 문제가 되므로, 이것도 교체해준다.
- 교체를 하게 되면 테이블도 바꿔야 하지만, 우선 테이블은 그대로 두었다가 Tree가 다 바뀌면 교체한다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/16.webp)

- 교체가 끝났고, 노드가 갖는 자식 노드의 개수도 더해졌다.
- 기존에는 a가 1이었지만 a가 0이 되었다.
- NYT는 1100이 되었고, 다른 것도 바뀌게 되었다.

이런 방식으로 쭉 진행하다보면, 아래의 결과가 나오게 된다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/17.webp)

⇒ 지금까지의 과정이 `Encoding` 과정이다.

---

- Decoding도 똑같지만, NYT만 주어진 상태에서 Input을 읽으면서 진행하면 된다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/18.webp)

⇒ Decoding을 할 때 중요한 것 : 처음에는 무조건 다섯 개를 읽어야 하고, 이후에 어떤 것이 들어오는지 판단할 때 중요한 것이 NYT가 들어왔는가 안들어왔는가이다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/19.webp)

- 이 상태에서 다음 input이 101000… 이렇게 가는데, 만약, 새로운 것이 들어왔다면 반드시 NYT의 현재 값인 0이 있어야 하는데, 이것이 없기 때문에 기존 값이 들어온 것을 알 수 있고, a를 추가해준다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/20.webp)

- 이후에는 01000100… 으로 가게 되는데, 이건 반드시 새로운 것이 들어온 경우이다. 그 이유가 무엇이냐면, 다음이 1이 아니기 때문에 반드시 새로운 것이 들어와야 하는데, 그렇다면 반드시 현재 0인 NYT가 들어오게 되고, 그렇다면 10001이 새로운 Symbol의 Code가 된다.

> 주어진 input을 따라 tree를 내려가면서 확인해보고, 있으면 기존 것이고 아니면 없는 것이라고 보면 된다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/21.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/22.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/23.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/24.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/25.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/26.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/27.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/28.webp)

⇒ 이와 같은 방식으로 문제를 해결하면 된다.

---

- 지금까지 공부한 것

    - Huffman-Coding
    - Extended Huffman-Coding
    - Adaptive Huffman-Coding

---

## Golomb Codes

Huffman-Coding과 함께 가장 많이 사용되는 압축 방식 중 하나이다. Unary Code에 Golomb Code가 사용된다.

- Unary Code

0 → 0

1 → 10

2 → 110

3 → 1110

이런 방식으로 코딩하는 것을 Unary Code라고 한다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/29.webp)

- 입력 값을 `quotient`(몫)과 `remainder`(나머지)로 나누기 위하여 Tunable Parameter ‘`m`’을 사용하게 된다.

    - q = n/m 을 floor 한 것. 몫을 의미한다. (quotient)
    - r = n - qm 나머지를 의미한다. (remainder)

이때 q를 **unary code**로 나타내기 위해서, 

- r 은 log2m bits
- m은 log2m의 ceiling을 사용한다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/30.webp)

- 상단의 비트에 대해서는 적은 비트를 할당할 수 있다.
- 하단의 비트에 대해서는 높은 비트를 할당할 수 있다.
- Golomb Code Example

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/31.webp)

log2m의 floor는 2, log2m의 ceiling은 3

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/32.webp)

이 내용에 따르면, 0 ≤ r ≤ 1 인 r에 대해서는 2 bit를 할당할 수 있고, r+2에 대해서는 3bit를 할당할 수 있는 것이다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/33.webp)

- m = 6이기 때문에, 나머지는 5까지 존재할 수 있다. 그러면 6부터는 몫이 하나 증가한다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/34.webp)

- q가 0일 때. 즉, 몫이 0일 때.

위의 조건에 따르면 0 ≤ r ≤ 1 일 때, 2 bit가 할당되고, 그 외에 대해서는 3bit가 할당된다.

따라서, q : 0, r : 0에 대해서는 0(q) 00, q : 0, r : 1에 대해서는 0(q) 01이 ‘`작은 것부터`’ 할당된다.

이후에는 ‘`큰 것부터`’ 0(q) 111, 110, 101, 100 로 줄여간다.

- q가 1일 때. 즉, 몫이 1일 때.

`unary`이기 때문에, 이 때는 앞에 `10`을 붙여주어야 한다. 그렇다면, q가 2일 때는 `110`일 것.

- Choosing ‘m’

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/35.webp)

위와 같은 input이 존재할 때, m을 정하는 공식을 활용하면, 2가 되는 것이 좋다고 알 수 있음.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/36.webp)

---

- Huffman Coding Example 1

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/37.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/38.webp)

- Self-Information 구하기 : -log2P

A : -0.387log2(0.387)

B : -0.194log2(0.194)

C : -0.161log2(0.161)

D : -0.129log2(0.129)

E : -0.129log2(0.129)

⇒ 이렇게 하면 I(x)가 나오게 된다.

- Huffman Coding을 통해 Code Length가 정해졌을 때, H(x)와 총 H를 구할 수 있다.

---

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/39.webp)

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/40.webp)

- 예를 들어 100에 대한 code words를 구하면 다음과 같다.  
    ⇒ n이 100이고 m은 5. 이 경우의 q는 20, r은 0이다.

![](../../../assets/img/study/major/embedded-vision/03-huffman-coding/41.webp)

- Golomb-Rice Code (Golomb Code의 특별한 형태)  
    ⇒ m이 2의 제곱 형태로 나와서, Floor와 Ceiling이 같아 줄어드는 형태가 없는 경우.
