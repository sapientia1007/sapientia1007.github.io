---
layout: post
title: Machine_Learning_Study 1
subtitle: Section 1/2/11
categories: Machine_Learning_Study
tags: Study
use_math : true
---

## Section 1 - Giving Computers the Ability to Learn from Data

**머신러닝**은 데이터를 활용하는 알고리즘의 과학과 적용인 모든 컴퓨터 과학의 가장 흥미로운 분야이다.

- 머신러닝의 일반적 개념
- 세 가지 유형의 학습과 기본 용어
- 머신 러닝 시스템을 성공적으로 설계하기 위한 구성 요소
- 데이터 분석 및 머신 러닝을 위한 Python 설치 및 설정

머신 러닝은 인간이 많은 양의 데이터를 분석하여 수동으로 규칙을 도출하고 모델을 구축하도록 요구하는 대신 예측 모델의 성능을 점진적으로 개선하고 데이터 중심 결정을 내리기 위해 데이터에 대한 지식을 포착하는 더 효율적인 대안을 제공한다.

Chap 1에서는 기계 학습의 세 가지 유형으로 지도 학습, 비지도 학습 그리고 강화 학습에 대해 살펴볼 것이다. 우리는 근본적인 차이점에 대해 배울 것이다. 

세 가지 다른 학습 유형 사이에서 그리고 개념적인 예를 사용하여, 우리는 그것들이 적용될 수 있는 실질적인 문제 영역에 대한 이해를 개발할 것이다.

**Supervised Learning(지도 학습)**
- Labeled data, 레이블이 지정된 데이터
- Direct feedback, 직접 피드백 
- Predict outcome/future, 결과와 미래 예측

**Unsupervised Learning(비지도 학습)**
- No labels/targets, 레이블과 대상이 없는 데이터
- No feedback, 피드백 없음
- Find hidden structure in data, 데이터에서 숨겨진 구조 찾기

**Reinforcement Learning(강화 학습)**
- Decision process, 의사결정 과정
- Reward system, 보상 제도
- Learn series of actions, 일련의 작업 학습


**지도 학습**의 주요 목표는 레이블이 지정된 훈련 데이터로부터 모델을 학습하는 것이다. 
- 보이지 않는 데이터나 미래의 데이터에 대해 예측한다.
- 여기서 "지도"라는 용어는 일련의 교육을 가리킨다. 
- 원하는 출력 신호(데이터 입력)가 이미 알려진 예제(데이터 입력)이다. 
- 지도 학습은 데이터 입력과 레이블 사이의 관계를 모델링하는 프로세스이다. 
- 스팸 필터와 같은 각각의 클래스 라벨을 가진 지도 학습은 **분류 작업**이라고도 한다.
- 다른 지도 학습의 하위 항목은 결과 신호가 지속적인 값인 **회귀**이다.


**분류**은 목표가 지난 경험에 근거한 데이터나 새로운 예시의 범주적 클래스 항목을 예측하는 것인 *지도 학습의 하위항목*이다.
- 이전에 언급된 스팸 이메일 감지의 예시가 이중 분류 작업의 전형적인 예를 표현하고, 그 머신러닝 알고리즘은 두개의 가능한 클래스인 스팸 메일과 스팸이 아닌 메일을 구별하는 일련의 규픽을 학습하는것이다.
- 다분위 분류의 전형적인 예는 손글씨 인식이다. 
- 만약 사용자가 손글씨를 입력 장치를 통해 예측한다면, 우리의 예측적인 모델은 특정한 정확성을 가진 알파벳에서 알맞은 문자를 예측할 수 있을것이다.

지도 학습의 두번째 유형은 **회귀 분석**이라 불리는 *지속적인 결과의 예측*이다.

머신러닝의 다른 유형은 **강화 학습**이다.
- 강화 학습에서, 그 목표는 환경과의 상호작용을 근거로 한 성능을 향상하는 시스템, 즉 agent를 발전시키는 것이다.
- 환경의 현재 상태에 대한 정보를 보상 신호로 받는다면, 우리는 지도 학습과 관련된 분야로서 강화 학습을 생각할 수 있다.
- 하지만, 일반적인 계획은 강화 학습의 agent를 환경과의 상호작용을 통해 보상을 최대화 하도록 노력하는 것이다.
- 요약하자면, 강화 학습은 즉각 조치를 취하거나 지연된 피드백을 통해 보상을 받을 수 있는 전체 보상을 최대화하는 행동을 선택하는 학습과 관련되어 있다.


감독 학습에서, 우리는 모델 훈련 전에 옳은 답을 알고 있고, 강화학습에서 우리는 agent에 수행되는 특정한 행동에 대한 보상을 정의한다.

하지만, 비감독 학습에서, 우리는 라벨 되지 않은 데이터나 알려지지 않은 구조의 데이터를 다룬다.

비감독 학습 기술을 사용하면서, 우리는 보상 기능이나 변화 결과의 지도 없이 의미 있는 정보를 추출하도록 탐구한다.






**Clustering(군집화)** 는 탐색적 데이터 분석 또는 패턴 발견 기법으로서 다음과 같은 기능을 구성할 수 있다. 
- 그룹 구성원 자격에 대한 사전 지식 없이 의미 있는 하위 그룹으로 정보를 쌓을 수 있다.
- 군집화는 구조화를 위한 훌륭한 기술이다.
- 정보 및 데이터로부터 의미 있는 관계를 도출할 수 있다.

비지도 차원 감소는 데이터에서 노이즈를 제거하기 위해 기능 전처리에서 일반적으로 사용되는 접근 방식으로, 특정 알고리즘의 예측 성능을 저하시킬 수 있다.


- 원래 데이터는 학습 알고리즘의 최적 성능에 필요한 형태와 형태로 오는 경우가 드물다. 따라서, 데이터의 전처리는 모든 기계 학습 애플리케이션에서 가장 중요한 단계 중 하나이다.
- 실제로 최상의 성능을 발휘하는 모델을 훈련하고 선택하기 위해서는 최소한 소수의 서로 다른 학습 알고리즘을 비교하는 것이 필수적이다.
- 문제를 해결하기 위해 "교차 검증"으로 요약되는 다양한 기술을 사용할 수 있다. 교차 검증에서 모델의 일반화 성능을 추정하기 위해 데이터 세트를 훈련 및 검증 하위 세트로 추가로 나눈다.
- 교육 데이터 세트에 적합한 모델을 선택한 후 테스트 데이터 세트를 사용할 수 있다. 
이 보이지 않는 데이터에 대해 얼마나 잘 수행하는지 추정하여 소위 일반화 오류를 추정한다.


우리는 지도 학습이 두 개의 중요한 하위 분야로 분류와 회귀로 나뉜다는 것을 배웠다.
분류 모델을 사용하면 객체를 알려진 클래스로 분류할 수 있지만, 목표 변수의 연속적 결과를 예측하기 위해서는 회귀 분석을 사용할 수 있다. 비지도 학습은 레이블이 없는 데이터에서 구조를 발견하는 데 유용한 기술을 제공할 뿐만 아니라 기능 전처리 단계에서 데이터 압축에도 유용할 수 있다.


## Section 2 - Training Simple Machine Learning Algorithms for Classification

우리는 알고리즘적으로 기술된 첫 번째 머신러닝 알고리즘 중 두 가지인 **perceptron**과 **적응형 선형 뉴런**의 분석을 사용한다.

인공지능(AI)을 설계하기 위해 생물학적 뇌가 어떻게 작동하는지를 이해하기 위해 Warren McCulloch과 Walter Pitts는 1943년에 소위 맥컬록 피트(MCP) 뉴런의 첫 번째 개념을 발표했다.

```python
# Python에서 perceptron을 수행하는 코드
import numpy as np
calss Perceptron : 
  def __init__(self, eta=0.01, n_iter=50, random_state=1) :
    self.eta = eta
    self.n_iter = n_iter
    self.random_state = random_state
    
  def fit(self, X, y) :
    regn = np.random.RandomState(self.random_state)
    self.w_ = rgen.normal(loc=0.0, scale=0.01, size=X.shape[1])
    self.b_ = np.float_(0.)
    slef.errors_ =[]
    for _ in range(self.n_iter) :
      errors = 0
      for xi, target in zip(X, y) :
        update = self.eta * (target - self.predict(xi))
        self.w_ += update * xi
        self.b_ += update
        errors += int(update != 0.0)
      self.errors_.append(errors)
    return self
    
  def net_input(self, X) :
    return np.dot(X, self.w_) + self.b_
    
  def predict(self, X) :
    return np.where(self.net_input(X) >= 0.0, 1, 0)
```
이 **perceptron 구현**을 사용하여, 이제 주어진 perceptron 객체를 사용하여 새로운 perceptron 객체를 **초기화**할 수 있다. 
- perceptron 구현을 테스트하기 위해, 다음의 분석과 예제를 제한할 것이다.  
- 이 장의 나머지 부분에는 두 개의 형상 변수가 포함된다. 
- 비록 perceptron 법칙은 2차원으로 제한하고, 꽃받침 길이와 꽃잎 길이, 두 가지 특징만을 고려하여, 학습 목적을 위해 산점도에서 훈련된 모델의 결정 영역을 시각화한다. 
- 우리는 또한 Iris 데이터 세트에서 setosa와 versicolor 두 가지 꽃 클래스만 고려할 것이다. 
- perceptron은 이진 분류기이다. 하지만 perceptron 알고리즘은 다중 클래스 분류로 확장될 수 있다.
- 다음으로, 50개의 Iris-setosa와 50개의 Iris-versicolor 꽃에 해당하는 첫 번째 100개의 클래스 레이블을 추출하고 클래스 레이블을 2개의 정수 클래스 레이블인 1(versicolor)과 0(setosa)로 변환한다. 
- 이 2차원 특징 부분 공간에서 선형 결정 경계는 setosa와 vertisicolor를 분리하기에 충분해야 한다.

```python
from matplotlib.colors import ListedColormap
def plot_decision_regions(X, y, classifier, resolution=0.02):
  markers = ('o', 's', '^', 'v', '<')
  colors = ('red', 'blue', 'lightgreen', 'gray', 'cyan')
  cmap = ListedColormap(colors[:len(np.unique(y))])
 
  x1_min, x1_max = X[:, 0].min() - 1, X[:, 0].max() + 1
  x2_min, x2_max = X[:, 1].min() - 1, X[:, 1].max() + 1
  xx1, xx2 = np.meshgrid(np.arange(x1_min, x1_max, resolution), np.arange(x2_min, x2_max, resolution))
  lab = classifier.predict(np.array([xx1.ravel(), xx2.ravel()]).T)
  lab = lab.reshape(xx1.shape)
  plt.contourf(xx1, xx2, lab, alpha=0.3, cmap=cmap)
  plt.xlim(xx1.min(), xx1.max())
  plt.ylim(xx2.min(), xx2.max())
 
  for idx, cl in enumerate(np.unique(y)):
    plt.scatter(x=X[y == cl, 0], y=X[y == cl, 1], alpha=0.8,
    c=colors[idx], marker=markers[idx], label=f'Class {cl}', edgecolor='black')
```
```text
먼저, 우리는 여러 가지 색상과 마커를 정의하고 나열된 색상 맵을 통해 색상 목록에서 색상 맵을 생성한다. 

그런 다음 두 기능에 대한 최소 및 최대 값을 결정하고 이러한 기능 벡터를 사용하여 NumPy meshgrid 함수를 통해 한 쌍의 그리드 배열 xx1과 xx2를 생성한다. 

두 가지 특징 차원에 대해 perceptron 분류기를 훈련시켰기 때문에, 예측 방법을 사용하여 해당 그리드 포인트의 클래스 레이블인 랩을 예측할 수 있도록 그리드 어레이를 평평하게 하고 Iris 훈련 하위 집합과 동일한 수의 열을 갖는 매트릭스를 생성해야 한다.

예측 클래스 레이블인 랩을 xx1 및 xx2와 동일한 치수의 그리드로 재구성한 후, 이제 Matplotlib의 등고선도를 통해 등고선도를 그릴 수 있다. 이 함수는 그리드 배열의 각 예측 클래스에 대해 서로 다른 결정 영역을 다른 색상으로 매핑한다.
```

지도 기계 학습 알고리즘의 핵심 요소 중 하나는 학습 프로세스 동안 최적화되어야 하는 정의된 목적 함수이다.

이 객관적 함수는 종종 우리가 최소화하고자 하는 손실 또는 비용 함수이다.

따라서, **gredient descent**라는 매우 간단하지만 강력한 최적화 알고리즘을 사용하여 Iris 데이터 세트의 예를 분류하기 위해 손실 함수를 최소화하는 가중치를 찾을 수 있다.

perceptron 규칙과 Adaline은 매우 유사하므로, 앞에서 정의한 perceptron 구현을 취할 것이고, 무게와 편향 매개변수가 현재가 되도록 적합 방법을 바꿀 것이다. 

radient descent을 통해 손실 함수를 최소화하여 업데이트된다.

또한 가중치 업데이트는 훈련 데이터 세트의 모든 예를 기반으로 계산되며, 이 접근 방식을 **배치 그레이디언트 강하**라고도 한다. 

이 장과 이 책의 뒷부분에서 관련 개념에 대해 이야기할 때 더 명확해지고 혼란을 피하기 위해, 우리는 이 프로세스를 **전체 배치 기울기 하강**이라고 지칭할 것이다.

```python
class AdalineGD : 
  def __init__(Self, eta=0.01, n_iter=50, random_state=1) :
    self.eta = eta
    self.n_iter = n_iter
    self.random_state = random_state
  
  def fit(self, X, y) :
    rgen = np.random.RandomState(self.random_state)
    self.w_ rgen.normal(loc=0.0, scale=0.01, size=X.shape[1])
    self.b_ = np.float_(0.)
    self.losses_ = []
    
    for i in range(self.n_iter) :
      net_input = self.net_input(X)
      output = self.activation(net_input)
      errors = (y - output)
      self.w_ += self.eta * 2.0 * X.T.dot(errors) / X.shape[0]
      self.b_ += self.eta * 2.0 * errors.mean()
      loss = (errors**2).mean()
      self.losses_.append(loss)
    return self

  def net_input(self, X) :
    return np.dot(X, self.w_) + self.b_
    
  def activation(self, X) :
    return X
  
  def predict(self, X) :
    return np.where(self.activation(self.net_input(X)) >= 0.5, 1, 0)
```    

활성화 방법은 단순히 식별 기능이기 때문에 코드에 영향을 미치지 않는다.

여기서는 단일 계층 NN을 통해 정보가 흐르는 방법과 관련된 일반적인 개념을 설명하기 위해 활성화 방법을 통해 계산된 활성화 함수(입력 데이터, 순 입력, 활성화 및 출력의 기능)를 추가했다. 

Gradient descent은 형상 스케일링의 혜택을 받는 많은 알고리즘 중 하나이다. 

이 섹션에서는 **표준화**라는 기능 확장 방법을 사용할 것이다. 

이 *정규화 절차는 경사 하강 학습이 더 빨리 수렴하는 데 도움*이 된다. 

그러나 원본 데이터 세트를 정상적으로 배포하지는 않는다. 

**표준화**는 각 형상의 평균을 이동시켜 중심점이 0이 되도록 하고 각 형상의 표준 편차는 1(단위 분산)이다.

**배치 경사 하강 알고리즘**의 일반적인 대안은 확률적 경사 하강(SGD)이며, 이는 때때로 반복적 또는 온라인 경사 하강이라고도 한다.

**SGD**의 또 다른 장점은 온라인 학습에 사용할 수 있다는 것이다. **온라인 학습**에서, 우리의 모델은 새로운 훈련 데이터가 도착함에 따라 즉시 훈련된다.



## Section 11 - Implementing a Multilayer Artificial Neural Network from Scratch




___

## 참고 : 
Machine Learning with Pytorch and Scikit-learn, Raschka, Liu, Mirjalili
