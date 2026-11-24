---
title: "Transformer와 PatchTST"
---

# Transformer와 PatchTST

2023년 11월, 인공지능 수업의 [PatchTST 성능 개선 프로젝트](../../projects/ai-course/03-patchtst.md)를 준비하면서 쓴 노트입니다. Transformer를 처음 공부하는 단계였기 때문에 Seq2Seq와 attention부터 시작해 Transformer의 구조를 강의로 익히고, 그다음 PatchTST의 공식 코드와 논문을 읽었습니다. 노트를 쓴 순서대로 열 편으로 나누었고, 공부하면서 남긴 질문은 각 글의 끝에 답과 함께 모았습니다.

**Transformer 익히기**

- [1. Seq2Seq와 Attention으로 보는 기계 번역 과정](01-seq2seq-attention.md): 단어별 번역의 한계, encoder-decoder 구조, 고정된 context vector의 문제, attention weight 계산 예제
- [2. Transformer 강의 정리 1: Seq2Seq의 한계에서 Multi-Head Attention까지](02-transformer-lecture-notes-1.md): positional encoding, encoder와 decoder의 연결, Query·Key·Value, scaled dot-product attention을 행렬 예제로 따라가기
- [3. Skip Connection: 왜 x + F(x)가 학습하기 쉬운가](03-skip-connection.md): identity를 만드는 두 가지 방법의 비교, residual learning이라는 이름의 의미
- [4. Transformer 강의 정리 2: 병렬 처리로 본 Self-Attention, Encoder, Decoder](04-transformer-lecture-notes-2.md): 같은 내용을 다른 강의로 다시 정리. attention score 계산 예제, label smoothing

**PatchTST 읽기**

- [5. PatchTST 공식 코드 분석: 실행 진입점에서 모델 구조까지](05-patchtst-code-analysis.md): 인자 처리, 실험 클래스, 데이터 불러오기, RevIN과 patching, encoder와 head
- [6. PyTorch 기본: nn.Module과 학습 루프](06-pytorch-nn-module.md): `forward()`가 호출되는 방식, 모델을 학습시키는 순서
- [7. 정규화 기법 네 가지: Batch, Layer, Instance, Group Normalization](07-normalization-techniques.md): 평균과 분산을 구하는 범위의 차이, 시계열에서의 instance normalization
- [8. 시계열 분해: 추세, 계절, 나머지](08-time-series-decomposition.md): 구성 요소, additive와 multiplicative, 이동평균
- [9. PatchTST의 forward 과정 따라가기](09-patchtst-forward-pass.md): 클래스별 `forward()` 호출 순서, encoder layer와 attention의 계산, tensor shape의 변화
- [10. PatchTST 논문 요약](10-patchtst-paper-summary.md): patching과 channel-independence, 관련 연구, 모델 구조를 shape으로 따져 보기

같은 주제를 나중에 다시 정리한 글도 있습니다.

- [Attention Is All You Need 리뷰](../../papers/2025-01-06-attention-is-all-you-need.md)
- [PatchTST: A Time Series Is Worth 64 Words 리뷰](../../papers/2024-11-25-patchtst.md)
- [ResNet: 깊은 네트워크를 학습시키는 residual learning](../ai-fundamentals/01-resnet.md)
