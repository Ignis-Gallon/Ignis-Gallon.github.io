---
title: "NumPy > ndarray"
date: 2023-07-16
last_modified_at: 2023-07-16
category: study
tags: [NumPy, ndarray, Python]
---

# NumPy > ndarray

[[ Python 3 ] Numpy 란 무엇인가? (ndarray 클래스)](https://supermemi.tistory.com/14)

- ndarray 클래스는 NumPy의 핵심 클래스이다. 이는 다차원 행렬 자료 구조를 지원한다.

    1. ndarray 클래스는 C언어의 array처럼 연속적인 메모리 배치를 가지기 때문에, 모든 원소가 같은 자료형으로 구성되어야만 한다. 이러한 제약을 가지는 대신, 내부의 원소에 대한 접근과 반복문 실행이 빨라진다.

    2. ndarray 클래스는 벡터화 연산(vectorized operation)을 지원한다.

        원소 모두를 제곱하기 위해서 반복문을 이용해 원소를 각각 곱할 필요 없이, 객체 자체를 제곱하는 것만으로도 원하는 결과를 얻을 수 있다.

- 다차원 행렬의 생성

ndarray는 N-dimensional Array의 약자이다. 단순 리스트와 유사한 1차원 행렬 이외에도 2차원, 3차원 행렬 등의 다차원 행렬 자료 구조를 지원한다.

⇒ 행렬 차원 및 크기는 ndim 속성과 shape 속성으로 알 수 있음.

---

- reshape : 데이터의 구조를 바꿔주는 함수. reshape 함수는 numpy.ndarray의 차원과 모양을 바꾸어준다.
