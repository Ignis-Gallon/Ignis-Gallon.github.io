---
title: "임베디드비젼시스템"
---

# 임베디드비젼시스템

3학년 1학기에 수강한 임베디드비젼시스템 수업의 주차별 노트입니다. 데이터 압축과 영상 압축을 다룬 과목으로, Entropy와 Huffman-Coding 같은 무손실 압축부터 Quantization, DCT, JPEG, Wavelets까지 정리했습니다.

- [3주차(Chpt. 1 : Modeling & Coding)](01-modeling-and-coding.md): 데이터 압축이 필요한 이유, Encoder와 Decoder의 기본 구조, Modeling과 Coding, Demosaicking
- [4주차(Chpt. 2 : Entropy)](02-entropy.md): Redundancy와 Information Theory, Self-Information과 Entropy, Markov Model, Prefix Code
- [5주차 (Chpt. 3 : Huffman-Coding)](03-huffman-coding.md): Shannon-Fano Coding, Huffman-Coding, Extended·Adaptive Huffman, Golomb Codes
- [6주차 (Chpt. 4 : Arithmetic Coding)](04-arithmetic-coding.md): 전체 메시지를 0과 1 사이의 실수(Tag) 하나로 나타내는 원리, PDF와 CDF를 이용한 Tag 생성 예제
- [6주차 (Chpt. 5 : Dictionary Techniques)](05-dictionary-techniques.md): 사전 기반 압축 방법, LZ77, LZ78, LZW의 Encoding 예제
- [7주차 (Chpt. 7 : Lossless Image Compression)](06-lossless-image-compression.md): 8개의 예측 모드를 쓰는 Lossless JPEG, Context-Adaptive 방식(CALIC)
- [8주차 (Chpt. 8 : Lossy Compression)](07-lossy-compression.md): Lossy Compression을 쓰는 이유, 왜곡을 재는 Squared error, MSE, SNR, PSNR
- [9주차 (Chpt. 9 : Quantization)](08-quantization.md): 큰 값의 집합을 대표값으로 줄이는 Quantization, Quantization Error, Uniform Quantizer
- [10주차 (Chpt. 10 : Differential Coding)](09-differential-coding.md): 현재 값과 이전 값의 차이를 부호화하는 Differential Coding, Quantization을 함께 쓸 때 누적되는 Error
- [11주차~13주차 (Chpt. 15 : Color Models, DCT, JPEG, Wavelets)](10-color-models-dct-jpeg-wavelets.md): 색과 빛의 기초, 색공간, QCIF 영상의 파일 구조, DCT와 JPEG, Wavelets Transform
