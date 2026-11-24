---
title: "PyTorch 실습을 위한 Anaconda 설정"
date: 2023-07-13
last_modified_at: 2023-11-24
category: study
tags: [PyTorch, Anaconda, 환경 설정]
---

# PyTorch 실습을 위한 Anaconda 설정

작성일 2023-07-13 · 최종 수정일 2023-11-24
{ .post-date }

[[Python] Anaconda 활용하여 Pytorch 설치하기](https://velog.io/@hyeongjun/Python-Anaconda-활용하여-Pytorch-설치하기)

[Pytorch(GPU)를 위한 Python 환경 구축 - conda 외 가상환경](https://qlsenddl-lab.tistory.com/59)

⇒ CUDA를 이용할 수 있는 방법까지 소개되어 있음.

[[Anaconda] Window에서 Anaconda로 Pytorch 사용하기](https://kk-programming.tistory.com/10)

[Install PyTorch 2.0 in Anaconda | Conda | PyTorch 2.0](https://www.youtube.com/watch?v=8MUuEmNFHZ0)

[Uninstall PyTorch in Anaconda | Conda | PyTorch 2.0](https://www.youtube.com/watch?v=ZSYdwgkEOZE)

---

- anaconda prompt를 실행한다.

conda info --env를 입력하면 현재 로컬에 존재하는 가상환경 목록을 확인할 수 있다.

1. conda create -n 이름 : 이 명령어를 통해서 가상 환경을 생성한다.

2. conda activate 이름 : 이 명령어를 통해서 가상 환경에 접속한다.

3. Pytorch 설치 : [https://pytorch.org/get-started/locally/](https://pytorch.org/get-started/locally/) 이곳에 접속해서 자신의 컴퓨터 세팅에 맞게 체크하면 설치를 할 수 있는 명령어가 뜬다. 해당 명령어를 anaconda prompt에 입력하면 됨.

4. 아래 코드를 입력해서 정상 동작을 확인하자.

    ```python
    python
    import torch
    torch.rand(10)
    ```

5. 잘 출력된다면 exit()를 입력하여 다시 Anaconda Prompt로 나갈 수 있다.
6. 가상환경을 제거하려면 conda remove -n 이름 --all을 입력하면 된다.
