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
이 perceptron 구현을 사용하여, 이제 주어진 perceptron 객체를 사용하여 새로운 perceptron 객체를 초기화할 수 있다. 

우리의 perceptron 구현을 테스트하기 위해, 다음의 분석과 예제를 제한할 것이다.  

이 장의 나머지 부분에는 두 개의 형상 변수(치수)가 포함된다. 

비록 perceptron 법칙은 2차원으로 제한하고, 꽃받침 길이와 꽃잎 길이, 두 가지 특징만을 고려하여, 학습 목적을 위해 산점도에서 훈련된 모델의 결정 영역을 시각화한다. 

우리는 또한 Iris 데이터 세트에서 setosa와 versicolor 두 가지 꽃 클래스만 고려할 것이다. 

perceptron은 이진 분류기이다. 하지만 perceptron 알고리즘은 다중 클래스 분류로 확장될 수 있다.
    













___

## 참고 : 
Machine Learning with Pytorch and Scikit-learn, Raschka, Liu, Mirjalili
