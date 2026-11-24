---
title: "Backpropagation"
date: 2023-07-15
last_modified_at: 2023-07-16
category: study
tags: [딥러닝, Backpropagation, Gradient Descent, TensorFlow]
---

# Backpropagation

작성일 2023-07-15 · 최종 수정일 2023-07-16
{ .post-date }

```python
from __future__ import absolute_import, division, print_function, unicode_literals

try:
  %tensorflow_version 2.x
except Exception:
  pass
```

```python
import tensorflow as tf
import numpy as np

# random seed for always same result from TF2
tf.random.set_seed(1)
np.random.seed(1)
```

```python
class Node:
    def __init__(self):
        # TensorFlow에서 변수를 선언하는 방법.
        # tf.Variable(...)
        # 1) 텐서의 형태 2) 평균 3) 표준편차
        # 현재는 텐서의 형태만 지정하고 나머지는 디폴트 값으로 설정.
        # Normal Distribution을 따름.
        self.w = tf.Variable(tf.random.normal([2, 1]))
        self.b = tf.Variable(tf.random.normal([1, 1]))

    def __call__(self, x):
        return self.preds(x)

    def preds(self,x):
        # forward propagation
        # 학습을 진행하는 방식.
        # weight, bias, activation function
        out = tf.matmul(x,self.w)
        out = tf.add(out, self.b)
        out = tf.nn.sigmoid(out)
        return out

    def loss(self, y_pred, y):
        # MSE를 구함.
        return tf.reduce_mean(tf.square(y_pred - y))

    def train(self, inputs, outputs, learning_rate):
        epochs = range(10000)
        for i, epoch in enumerate(epochs):
            with tf.GradientTape() as t:
                # self.preds를 통해서 얻어낸 out과 label을 비교하여 loss(MSE) 얻어냄.
                current_loss = self.loss(self.preds(inputs), outputs)
                if i % 1000 == 0:
                    print(str(i) + " epoch, loss: "+str(current_loss.numpy()))
                # back propagation
                # 현재 MSE를 바탕으로 t.GradientTape().gradient()를 통해서
                # loss를 미분한 값을 구함.
                dW, db = t.gradient(current_loss, [self.w, self.b])

                # 현재 가중치와 편향값에 대해서 값 업데이트.
                self.w.assign_sub(learning_rate * dW)
                self.b.assign_sub(learning_rate * db)
```

⇒ 하나의 노드를 임의로 만들어버린 것.

node의 preds를 통해 한 번의 학습을 진행한다. 이때, 사전에 설정해둔 Random한 parameter를 통해서 weight를 곱하고 bias를 더해준 다음, sigmoid로 넘겨준다. 이 값과 output(Label) 과의 Loss값을 구하고, 이 Loss값을 통해서 backpropagation을 함으로써 weight와 bias의 value를 update한다. 이 과정을 정해진 epochs만큼 반복.

```python
# AND operation
inputs = tf.constant([[0.0,0.0], [0.0,1.0], [1.0,0.0], [1.0,1.0]])
# scalar를 item으로 갖는 2d tensor, 그걸 item으로 갖는 3d tensor.

outputs = tf.constant([[0.0], [0.0], [0.0], [1.0]])
# 각각의 2d tensor에 대응되는 output label.

node = Node()
# train

node.train(inputs, outputs, 0.01)
# test

# python에서 assert는 어떠한 값이 참임을 보증하는 '방어적 프로그래밍' 기법.
# 현재 activation function으로 sigmoid를 쓰고, sigmoid 값이 0.5 이하이면 0
# sigmoid 값이 0.5 이상이면 1이라고 판단한다. (일종의 threshold)
# 따라서 각 node의 결과가 예상된 결과라고 확신하는 것.

# Class Node()는 __call__ 메서드로 self.preds()를 진행.
# 이는 하나의 input vector (1x4)에 대한 output을 내놓는 것이므로,
# 결과값을 판단하는 부분이라고 볼 수 있음.
assert node([[0.0,0.0]]).numpy()[0][0] < 0.5
assert node([[0.0,1.0]]).numpy()[0][0] < 0.5
assert node([[1.0,0.0]]).numpy()[0][0] < 0.5
assert node([[1.0,1.0]]).numpy()[0][0] >= 0.5
```

```python
# OR operation
inputs = tf.constant([[0.0,0.0], [0.0,1.0], [1.0,0.0], [1.0,1.0]])
outputs = tf.constant([[0.0], [1.0], [1.0], [1.0]])

node = Node()
# train
node.train(inputs, outputs, 0.01)
# test
assert node([[0.0,0.0]]).numpy()[0][0] < 0.5
assert node([[0.0,1.0]]).numpy()[0][0] >= 0.5
assert node([[1.0,0.0]]).numpy()[0][0] >= 0.5
assert node([[1.0,1.0]]).numpy()[0][0] >= 0.5
```

```python
print("Node Weights: ",node.w.numpy())
print("Node Bias: ",node.b.numpy())
```
