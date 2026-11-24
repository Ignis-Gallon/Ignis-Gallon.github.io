---
title: "6주차 (Chpt. 5 : Dictionary Techniques)"
date: 2023-05-25
last_modified_at: 2025-05-24
category: major
tags: [임베디드비젼시스템, 데이터 압축, LZ77, LZ78, LZW]
---

# 6주차 (Chpt. 5 : Dictionary Techniques)

작성일 2023-05-25 · 최종 수정일 2025-05-24
{ .post-date }

## Dictionary-based Compression Methods

이전에 만들었던 것을 사전으로 만들어 저장할 수 있다면, 압축하는데 큰 도움이 될 것이다. 만약에 통계 모델을 사용하지 않거나 variable-size codes를 사용하지 않으면 압축이 어렵다.

dictionary는 symbol이 들어올 때마다 update되기 때문에, adaptive한 형태가 될 것이다.

- 자주 나오는 패턴을 파악한다.
- 이것들은 효율적으로 Encoding 한다.
- 나머지는 대충 Coding 한다.
- 이런 Coding은 Random Data에는 사용하지 못하는데, Dictionary를 만드는 것 자체가 큰 에너지를 필요로 하지만, Random Data는 빈도의 의미가 퇴색되는 데이터이기 때문이다.

---

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/01.webp)

- 26개의 Alphabet 문자와 6개의 기호가 있다고 가정해보자.
- 32개의 Symbol이 있을 때는 5bit가 필요하게 될 것이다.
- 그런데, 만일 네 개의 Symbol Pattern을 가진다고 하면, 하나의 symbol에 20 bits를 할당할 수 있게 된다.
- 우리가 만약에 256개의 가장 많이 나오는 패턴을 파악할 수 있다면, 그것을 8 bits로 표현하고, 그 외에는 20 bits로 표현하면 어떨까?
- 이때, 들어오는 패턴이 256개의 패턴에 포함되는지 안되는지를 파악하기 위하여 1-bit prefix를 추가할 수 있다.
- Motivating Example

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/02.webp)

‘p’가 dictionary에 포함될 확률이라고 가정하면, 우선 그것을 파악하기 위해 1 bit가 필요하게 되기 때문에, dictionary에 포함되는 것은 9bits가 되고, 포함되지 않는 것은 21bits가 된다. 이때, dictionary에 포함될 확률이 p이기 때문에 포함되지 않을 확률은 1-p이고, 이것들에 대하여 Bit rate를 구하는 공식을 적용하면, 아래와 같은 값이 나오게 된다.

R = 9p + 21(1-p) = 21 - 12p

- `압축이 효과를 얻기 위해서는 21 - 12p < 20을 만족해야 한다.`

    - 왜냐면, 압축하기 이전에 아무 것도 고려하지 않았을 때가 20 bits였기 때문에, 압축을 한 결과가 최소 20 bits보다는 작아야 효과가 있다고 생각할 수 있다.

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/03.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/04.webp)

---

## LZ77, LZ78, LZW

- LZ77 : 이전에 Encode된 Sequence를 Dictionary로 삼는 것. Sliding Window Compression이라고 할 수 있다. 다른 말로, Window를 몇 개의 종류로 지정하고, 이것이 이동하면서 압축이 진행되는 형태이다.

- Mechanism

    - Search Buffer
    - Look-Ahead Buffer
    - Search Pointer

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/05.webp)

- Search Pointer가 a에 위치하고 있고, 현재 Search Pointer와 가장 멀리 떨어져 있으면서 Search Pointer가 가리키는 값과 같은 것이 Match Pointer이다.
- 해당 예제에서는 Search Buffer가 8개이고, Look-Ahead Buffer가 7개이다.
- (o)ffset = search ptr - match ptr = 7
- (l)ength = number of consecutive letters matched = 4
- (c&#41;odeword(r&#41; : 이번에 copy하지 못하는 것. 현재는 abra 이후의 r을 의미함.
- Encoding

인코딩을 어떻게 하냐면, 7개만큼 가서, 네 개를 copy하고, 다음의 code word를 ‘r’이다. 라고 하면, 다섯 개가 한번에 codewords가 되는 것이다.

&lt;o, l, c&gt; = &lt;7, 4, C(’r’)&gt;

- 만약 |search buff| = S, |A| = A, S + |Look-Ahead Buff| = W라고 한다면,

    - Fixed Length Encoding은 아래와 같다.

    ![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/06.webp)

- Encoding Example (LZ77)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/07.webp)

- W = 13 (윈도우의 크기는 13)
- S = 7 (Search Buffer는 7개)
- 따라서, **Look-Ahead Buffer의 크기**는 13-7인 6이다.
- 처음에 d는 매칭되는 것이 없으므로 코딩이 불가능하다. 오히려 손실이 발생하게 됨.
- 따라서 send &lt;0, 0, C(d)&gt;

- 이제 윈도우를 하나 이동시킨다.

    - |abracad|abrarr| arrad

⇒ 이때, 매칭되는 a가 여러 개 있는데, Match Pointer는 현재 Search Buffer에서 가장 왼쪽에 있는 것이므로, 아래에 대해서 Encoding 해주어야 한다.

|abracad|abrarr|arrad

- Send &lt;7, 4, C(r&#41;&gt;
- Search Buffer에서 7개 만큼 앞으로 가서 4 개를 Copy 하고, 그 다음에 r을 지정해 준다.
- C(’r’)도 codewords가 되므로, 같이 날라간다.

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/08.webp)

- 한번 코딩을 하고 나면, 코딩된 codewords만큼 왼쪽으로 이동시킨다. 이전에 coding한 codewords가 abra C(’r’) 이었으므로, 이만큼을 왼쪽으로 밀어서 현재 Search Buffer는  
    |cadabrar|이 되고, Look-Ahead Buffer에서 &lt;3, 5, C(’d’)&gt;로 coding이 가능하다.

    - 이는 Search Buffer에서 연속적으로 이어질 수 있다면, Look-Ahead Buffer를 침범해서 Coding이 만들어질 수 있다는 것을 의미한다.

- LZ77에서 나올 수 있는 케이스는 아래 세 개와 같다.

    - No Match : &lt;0, 0, C(’x’)&gt;
    - Match : &lt;o, l, c&gt;
    - Match beyond the end of search buffer

---

- Decoding Example of LZ77

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/09.webp)

- Input이 위와 같이 나왔고, **Output이 제시되어 있다.** 이걸 Decode해 보도록 한다.
- Output은 초기 Encoding 할 때 주어진 Search Buffer와 같은 것으로 보임.
- &lt;0, 0, C(’d’)&gt;이므로, d 하나에 대해서 밀어준다.
- &lt;7, 4, C(’r’)&gt; 이므로, 우측에서부터 시작해 7개의 글자를 가고, 네 개를 copy한 뒤, r을 추가한다. 그러므로, cabrac|adabrar이 된다.
- &lt;3, 5, C(’d’)&gt; 이므로, 먼저 세 개를 copy하고, 다시 가장 우측의  두 개를 copy 한 다음, d를 추가해주면 된다. 그러면 cabracadabr|arrarar|이 된다.

⇒ Decode C(d) : cabracadabrarrarard

- LZ77의 장단점

    - Adaptive Scheme이기 때문에, 사전 지식이 전혀 필요하지 않다.
    - 바로 직전의 Local 정보만 이용한다. (Locality)
    - Locality가 있음에도 불구하고, 충분히 가까이 있지 않는다면, 크게 효과를 보기 어렵다.

---

## LZ78

LZ77은 최근에 나온 것이 자주 나와야 이득을 본다. 하지만, 최근에 나온 것이 자주 나온다는 보장이 없기 때문에 LZ78에서는 Search Buffer을 없애고 Dictionary를 추가해 버린다.

따라서 Encoder/Decoder가 dictionary를 사용하고, Sync가 있어서 동기화가 된다. 따라서 실시간으로 Update가 되어야 한다.

- Encoding : &lt;i, c&gt;

**LZ78 Example (Encode)**

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/10.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/11.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/12.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/13.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/14.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/15.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/16.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/17.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/18.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/19.webp)

이와 같은 방법으로 LZ78의 Encoding을 생성할 수 있다.

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/20.webp)

- LZ78의 특징

    - Dictionary가 너무 커지는 것을 막기 위해서, 가지를 잘라주듯이 일부 Row를 잘라주거나, 전체를 reset하는 방법 등이 있다.

---

## LZW

LZ78에서는 &lt;i, c&gt; 가 되었지만, 이 방법은 c조차 없애버리고 i만 사용하게 된다.

**LZW Encoding Example**

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/21.webp)

- LZW는 Table을 알고 있어야만 한다. 그래야 Coding이 가능하다.
- 여기서의 규칙은, Table이 들어올 때마다 Table이 Update되어야 한다.

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/22.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/23.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/24.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/25.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/26.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/27.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/28.webp)

- 이제부터는 wa가 존재하기 때문에, wab로 Table에 추가해준다.
- 이와 같은 방식으로 쭉 LZW Encoding을 하게 되면 아래와 같은 결과가 나온다.

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/29.webp)

**LZW Decoding**

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/30.webp)

Symbol이 a, b로 이루어져 있다고 할 때, input 값이 abababab…와 같은 형태라면 어떻게 될까?

**LZW Special Case Encoding**

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/31.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/32.webp)

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/33.webp)

- 이런 식으로 쭉 가면서, Table이 무한대로 커지게 된다.
- 따라서 이를 방지할 방법이 필요하게 된다.

![](../../../assets/img/study/major/embedded-vision/05-dictionary-techniques/34.webp)

- 그러므로 Decoding을 할 때, Table이 무한대로 길어지지 않도록 하기 위하여 특별한 조건을 추가해 주어야 한다.

---

## Summary

- Adaptive Techniques

    - LZ77
    - LZ78
    - LZW
