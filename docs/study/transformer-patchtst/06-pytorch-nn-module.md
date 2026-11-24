---
title: "PyTorch 기본: nn.Module과 학습 루프"
date: 2023-11-24
last_modified_at: 2023-11-24
category: study
tags: [PyTorch, nn.Module, DataLoader, 학습 루프]
---

# PyTorch 기본: nn.Module과 학습 루프

작성일 2023-11-24
{ .post-date }

[PatchTST 공식 코드](05-patchtst-code-analysis.md)를 읽다가, 모델 객체에 데이터를 넣기만 하면 여러 클래스의 계산이 차례로 실행되는 것이 이해되지 않아 PyTorch에서 모델을 만들고 학습시키는 기본 형태를 따로 정리한 노트이다.

## nn.Module을 상속받는 클래스

PyTorch에서 모델을 만들 때는 내장 모델이든 직접 만든 모델이든 같은 형태를 따른다.

1. `torch.nn.Module`을 상속받는다.
2. `__init__()`과 `forward()`를 다시 정의(override)한다.

`__init__()`에서는 모델이 사용할 module과 활성화 함수 등을 정의하고, `forward()`에서는 모델이 실행해야 하는 계산을 정의한다. 입력을 넣었을 때 어떤 과정을 거쳐 출력이 나오는지를 적는 곳이 `forward()`이다. 역전파 계산은 `backward()`를 호출하면 자동으로 이루어지므로 `forward()`만 정의하면 된다.

`nn.Module`을 상속받은 클래스의 객체는 `model(x)`처럼 함수를 부르듯 호출할 수 있고, 이렇게 호출하면 그 클래스의 `forward()`가 실행된다. `forward()` 안에서 다른 module을 같은 방식으로 호출하면 그 module의 `forward()`가 이어서 실행된다. 그래서 가장 바깥의 모델 객체에 데이터를 한 번 넣는 것만으로 안쪽 module들의 계산이 차례로 수행된다.

## 모델을 학습시키는 순서

### 준비

**1) 모델 클래스를 `nn.Module`을 상속받아 만든다.**

```python
class BinaryClassifier(nn.Module):
```

**2) `__init__()`에서 layer를 정의한다.**

```python
self.fc1 = nn.Linear(input_size, output_size)
```

`nn.Linear()` 등으로 모델에 layer를 차례로 쌓는다.

**3) `forward()`를 다시 정의한다.** 모델이 입력을 받았을 때 수행할 연산을 적는다.

**4) optimizer를 정의한다.**

```python
optimizer = optim.Adam(model.parameters(), lr=0.001)
```

**5) 손실 함수(criterion)를 정의한다.**

```python
criterion = nn.BCELoss()
```

**6) DataLoader를 정의한다.**

```python
dataloader = DataLoader(dataset, batch_size=10, shuffle=True)
```

DataLoader는 데이터를 다루기 쉽게 해 준다. 데이터를 `batch_size`만큼씩 나누고, epoch마다 섞을 수 있다. worker 수를 지정하면 큰 데이터를 비동기로 빠르게 불러올 수도 있다.

### 학습

**7) epoch 수를 정하고 for 문으로 학습을 진행한다.**

```python
num_epochs = 100

model.train()    # 모델을 train 모드로 설정

for epoch in range(num_epochs):
    for inputs, labels in dataloader:
        # 아래 8) ~ 10)이 들어간다
```

DataLoader를 for 문으로 돌면 batch가 하나씩 차례로 나온다. batch마다 아래 세 단계를 수행한다.

**8) gradient를 초기화한다.**

```python
optimizer.zero_grad()
```

**9) 모델에 입력을 넣어 forward를 진행하고 loss를 구한다.**

```python
outputs = model(inputs)
loss = criterion(outputs, labels)
```

**10) backward를 진행한다.**

```python
loss.backward()     # gradient 계산
optimizer.step()    # parameter 갱신
```

PatchTST의 학습 코드도 이 형태를 그대로 따른다. `Exp_Main.train()`에서 optimizer와 손실 함수, DataLoader를 준비하고, epoch과 batch에 대한 이중 for 문 안에서 `zero_grad()`, forward, `backward()`, `step()`을 차례로 호출한다.

## 참고 자료

- PyTorch 공식 문서, "torch.nn.Module". <https://pytorch.org/docs/stable/generated/torch.nn.Module.html>
- PyTorch 공식 튜토리얼, "Learn the Basics". <https://pytorch.org/tutorials/beginner/basics/intro.html>
