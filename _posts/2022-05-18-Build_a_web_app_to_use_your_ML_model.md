---
layout: post
title: 머신러닝 모델을 사용하여 Web App 생성하기
subtitle: Machine_Learning_Assignment9
categories: Machine_Learning
tags: Machine_Learning
use_math: true
---

### INTRO

📕 이 섹션에서는 ML 항목에 적용된 `Scikit-learn 모델`을 웹 응용 프로그램 내에서 예측하는 데 사용할 수 있는 파일로 저장하는 방법을 알아볼 것이다. 
  - 모델이 저장되면 **Flask**에 내장된 **웹 앱**에서 모델을 사용하는 방법을 배울 수 있다.

📕 이 섹션에서 학습할 내용 :
- **훈련 모델**을 **"pickle"** 하는 방법
- **Flask App**에서 **모델을 사용**하는 방법 

###  고려 사항 

- `모바일 앱`을 생성하거나 `IoT Context 모델`을 사용하는 것을 필요로 한다면, **TensorFlow Lite**를 사용할 수 있고 **Android나 iOS 앱**에서 모델을 사용할 수 있다.
- 모델이 **cloud**와 **locally** 중 어디에 속할지 생각할 수 있다.
- 앱은 오프라인에서도 동작 가능하다.
- **모델을 훈련**하기 위해 사용되는 **기술**은 사용할 도구에 영향을 끼칠 수 있다.
- 모델을 훈련하기 위해 사용되는 기술 : 
  - `TensorFlow`
  - `PyTorch`
  - `Lobe.ai`, `Azure Custom Vision`

___

## 참고 :
💥 **위의 모든 내용은 [ML-For-Beginners의 자료](https://github.com/codingalzi/ML-For-Beginners/tree/main/3-Web-App)를 참고하여 작성했습니다**

💥 추가 참고 자료 : [Flask Web Framework](https://opentutorials.org/course/4904)
