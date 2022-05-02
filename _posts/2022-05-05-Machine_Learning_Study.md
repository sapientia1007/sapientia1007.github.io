---
layout: post
title: Machine_Learning_Study 1
subtitle: Chap1/2/11
categories: Machine_Learning_Study
tags: Study
use_math : true
---

## Chap 1 - Giving Computers the Ability to Learn from Data

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

보이지 않는 데이터나 미래의 데이터에 대해 예측한다.

여기서 "지도"라는 용어는 일련의 교육을 가리킨다. 

원하는 출력 신호(데이터 입력)가 이미 알려진 예제(데이터 입력)이다. 

지도 학습은 데이터 입력과 레이블 사이의 관계를 모델링하는 프로세스이다. 

33p Classification for predicting class labels
___

## 참고 : 
Machine Learning with Pytorch and Scikit-learn, Raschka, Liu, Mirjalili
