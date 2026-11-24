---
title: "정규화 기법 네 가지: Batch, Layer, Instance, Group Normalization"
date: 2023-11-24
last_modified_at: 2023-11-24
category: study
tags: [Normalization, Batch Normalization, Layer Normalization, Instance Normalization, RevIN]
---

# 정규화 기법 네 가지: Batch, Layer, Instance, Group Normalization

작성일 2023-11-24
{ .post-date }

[PatchTST 공식 코드](05-patchtst-code-analysis.md)에 나오는 RevIN(Reversible Instance Normalization)을 이해하려고, instance normalization이 다른 정규화 기법과 어떻게 다른지 블로그 글을 보고 짧게 정리한 노트이다.

네 기법은 모두 feature를 평균 0, 분산 1이 되도록 정규화한다. 차이는 평균과 분산을 어느 범위에서 구하느냐에 있다. 이미지를 다루는 CNN의 feature가 (batch, channel, 높이, 너비) 모양이라고 할 때 다음과 같이 나뉜다.

| 기법 | 평균과 분산을 구하는 범위 |
|---|---|
| Batch Norm | mini-batch 전체에 걸쳐, channel 하나씩 |
| Layer Norm | instance 하나 안에서, 모든 channel을 합쳐서 |
| Instance Norm | instance 하나 안에서, channel 하나씩 |
| Group Norm | instance 하나 안에서, channel을 N개의 그룹으로 묶어 그룹 하나씩 |

- **Batch Norm**은 mini-batch에 들어 있는 모든 sample에서 같은 channel의 값을 모아 정규화한다.
- **Layer Norm**은 sample 하나에서 나온 feature를 channel 구분 없이 한 번에 정규화한다.
- **Instance Norm**은 sample 하나의 channel 각각을 따로 정규화한다. 즉 instance 안에 있는 channel마다 자기 값만으로 정규화한다.
- **Group Norm**은 sample 하나의 channel을 N개의 그룹으로 묶어 그룹 단위로 정규화한다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. instance normalization을 시계열 데이터에는 어떻게 적용하는가. channel은 있지만 이미지처럼 너비와 높이가 없다.**

이미지에서 너비와 높이가 하던 역할을 시계열에서는 시간 축이 한다. 시계열 입력은 (batch, 시간, 변수) 모양이고, 변수가 channel에 해당한다. RevIN은 입력 window 하나를 instance로 보고, 변수마다 시간 축 방향의 평균과 표준편차를 구해 정규화한다. 그리고 모델의 출력에는 저장해 둔 평균과 표준편차를 써서 반대 과정을 적용해 원래 scale로 되돌린다. PatchTST 코드에서 RevIN이 어떻게 쓰이는지는 [코드 분석](05-patchtst-code-analysis.md)에 적었다.

## 참고 자료

- "Batch Norm, Layer Norm, Instance Norm, Group Norm" (블로그). <https://dongsarchive.tistory.com/74>
- Y. Wu, K. He, "Group Normalization", ECCV 2018. <https://arxiv.org/abs/1803.08494>
- T. Kim, J. Kim, Y. Tae, C. Park, J.-H. Choi, J. Choo, "Reversible Instance Normalization for Accurate Time-Series Forecasting against Distribution Shift", ICLR 2022. <https://openreview.net/forum?id=cGDAkQo1C0p>
