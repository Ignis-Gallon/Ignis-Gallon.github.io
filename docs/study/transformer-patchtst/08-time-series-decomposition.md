---
title: "시계열 분해: 추세, 계절, 나머지"
date: 2023-11-24
last_modified_at: 2023-11-24
category: study
tags: [시계열, 시계열 분해, 이동평균, Additive, Multiplicative]
---

# 시계열 분해: 추세, 계절, 나머지

[PatchTST 공식 코드](05-patchtst-code-analysis.md)에 있는 분해(decomposition) 옵션을 이해하려고, 시계열 분해를 다룬 강의를 보고 짧게 정리한 노트이다. 이 옵션을 켜고 진행한 실험은 [PatchTST 성능 개선 실험](../../projects/ai-course/03-patchtst.md)에 있다.

## 신호와 잡음을 나누기

통계적인 예측에서 반드시 해야 하는 일은 신호(signal)와 잡음(noise)을 분리하는 것이다. 잡음을 걷어 낸 신호를 바탕으로 미래의 신호를 추정(extrapolate)하는 것이 목표이고, 시계열에서는 이것을 forecasting이라고 부른다.

## 시계열의 구성 요소

시계열 $y_t$는 세 가지 구성 요소로 나누어 본다.

- $T_t$: 추세(trend). 추세-주기 구성 요소
- $S_t$: 계절(seasonal) 구성 요소
- $E_t$: 오차(error). 관측값 $y_t$에서 위의 두 요소를 뺀 나머지이고, residual이라고도 한다

## 분해 방식

관측값을 구성 요소들의 합으로 볼지 곱으로 볼지에 따라 두 가지 방식이 있다.

$$
y_t = T_t + S_t + E_t \quad \text{(additive)}
$$

$$
y_t = T_t \times S_t \times E_t \quad \text{(multiplicative)}
$$

계절 변동의 크기나 추세-주기의 변동이 시간에 따라 달라지지 않으면 additive 방식이 적절하다. 계절 패턴의 변동이나 추세-주기의 변동이 시간이 흐르면서 달라지면 multiplicative 방식이 더 적절하다. 간단히 말해 변동의 폭(분산)이 일정하면 additive, 변하면 multiplicative를 쓴다.

## 분해하는 방법

전통적인 방법은 이동평균(moving average)으로 추세를 구해 분해하는 것이다. 더 현대적인 방법으로는 LOESS(Locally Estimated Scatterplot Smoothing) 회귀를 이용하는 STL(Seasonal-Trend decomposition using LOESS)이 있다.

PatchTST 코드의 분해 옵션은 이동평균을 쓰는 쪽이다. 입력에 이동평균을 적용한 결과를 추세로, 입력에서 추세를 뺀 값을 나머지로 놓는 additive 분해이다.

## 참고 자료

- "시계열 분석 첫걸음 - 11강 시계열 분해 이해하기 part1 | 이동평균의 이해" (YouTube). <https://www.youtube.com/watch?v=51x3K2omB-A>
- R. J. Hyndman, G. Athanasopoulos, "Forecasting: Principles and Practice" (3rd ed.), 3장 "Time series decomposition". <https://otexts.com/fpp3/decomposition.html>
