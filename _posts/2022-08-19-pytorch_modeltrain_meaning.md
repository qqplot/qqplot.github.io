---
layout: post
title:  "torch에서 model.train()과 model.eval() 의미"
categories: [ datascience ]
tags: [ pytorch, torch ]
fullview: false
comments: true
---


텐서(Tensor)란 torch에서 사용하는 특수한 자료구조이다. PyTorch에서는 텐서를 사용하여 모델의 입력과 출력, 모델의 파라미터들을 인코딩한다. 이는 NumPy와 API는 유사하지만, 텐서는 GPU나 다른 하드웨어 가속기에서 실행할 수 있다. 그리고 자동 미분에 최적화되어 있다.

---


# Tensor

텐서(Tensor)란 torch에서 사용하는 특수한 자료구조이다. PyTorch에서는 텐서를 사용하여 모델의 입력과 출력, 모델의 파라미터들을 인코딩한다. 이는 NumPy와 API는 유사하지만, 텐서는 GPU나 다른 하드웨어 가속기에서 실행할 수 있다. 그리고 자동 미분에 최적화되어 있다.

딥러닝에서 가장 자주 사용되는 알고리즘은 역전파이다. 이는 결국 체인룰을 이용한 미분 계산이기 때문에 딥러닝 프레임워크를 구현할 때에는 미분 계산과 그 결과를 저장해 놓는 것이 반복된다. 그래서 torch에서는 tensor에 `torch.autogard`로 불리는 자동 미분 엔진 내장해두었다.

세부적으로는, `requires_grad=True`인 모든 텐서들은 연산 기록을 추적하고 변화도 계산을 지원한다. 또한 같은 이유로 `backward()`라는 함수를 구현해놓지 않더라도, 호출만 하면 손실함수의 derivative를 계산해준다. 


```python
loss.backward()
print(w.grad)
print(b.grad)
```

<br/>

그러나 모델을 학습한 뒤 입력 데이터를 단순히 적용하기만 하는 경우와 같이 순전파 연산만 필요한 경우에는, 이러한 추적이나 지원이 필요없을 수 있다. 연산 코드를 `torch.no_grad()` 블록으로 둘러싸서 연산 추적을 멈출 수 있다. 혹은 `detach()` 메소드를 사용해도 된다.

```python
z = torch.matmul(x, w)+b
print(z.requires_grad) # True


with torch.no_grad():
    z = torch.matmul(x, w)+b
print(z.requires_grad) # False

z = torch.matmul(x, w)+b
z_det = z.detach()
print(z_det.requires_grad) # False

```

변화도 추적을 멈춰야 하는 이유는 파라미터를 업데이트하면 안되거나, 연산 속도를 위함이다.
pre-train 모델을 fine tuning할 때에는 기존 학습된 파라미터는 업데이트하는 걸 원하지 않을 것이다. 또한 변화도를 추적하지 않는 텐서의 연산이 더 효율적이기 때문에, 순전파 단계만 수행할 때 연산 속도가 향상된다.


<br/>


# Train 해야할 때와 Test 해야할 때 다른 점

모델을 만들어 두었다면 각 레이어마다 autograd를 껐다 켰다하는 것을 관리하기 힘들다. 그래서 `nn.Module`에는 train time과 evaluate time에 수행하는 다른 작업을 switching해줄 수 있도록하는 함수를 제공한다. 

train time과 evaluate time에 서로 다르게 동작해야 하는 것들에는 대표적으로 아래와 같은 것들이 있다.

- Dropout layer
- BatchNorm layer



<br/>


# 결론은 모드 변경

실제 API를 살펴보면 `eval()`은 사람이 보기 편하라고 만든 함수이다. 사실은 `train` 함수를 호출하고 모드를 껐다 켰다하는 것에 지나지 않는다.


```python
def eval(self: T) -> T:
    return self.train(False)
```

model.eval()을 수행하고 나서 다시 train 모드로 변경해주어야 한다.


```python
# train mode
model.train()

```


# 참고
- https://stackoverflow.com/questions/60018578/what-does-model-eval-do-in-pytorch/60018731#60018731
- https://pytorch.org/docs/stable/generated/torch.nn.Module.html
