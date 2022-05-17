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
- **모델을 훈련**하기 위해 사용되는 **기술**은 다음과 같다. 
*(이 기술은 사용할 도구에 영향을 끼칠 수 있다.)*
  - `TensorFlow`
  - `PyTorch`
  - `Lobe.ai`, `Azure Custom Vision`
- 웹 브라우저에서 모델 자체를 교육할 수 있는 **전체 Flask web app**을 만들 수 있다.
  - 이 작업은 JavaScript Context에서 Tensorflow.js를 사용하여 수행할 수 있다.

**Python 기반 노트북으로 작업해왔기때문에 이런 노트북에서 Python이 구축한 웹 앱에서 읽을 수 있는 형식으로 훈련된 모델을 내보내기 위해 필요한 단계를 알아볼 것이다.**

### 도구

이 작업을 수행하기 위해서는 **두 가지의 도구**가 필요하다.
- **Flask**
- **Pickle**

❗ 제작자들에 의해 'micro-framework'로 정의되는 `Flask`는 **파이썬과 웹 페이지를 구축**하기 위한 **템플릿 엔진**을 사용하는 <u>웹 프레임워크의 기본 기능을 제공한다.</u>

❗ <u>Python 객체 구조를 직렬화하고 역암호화</u>하는 Python 모듈을 `Pickle`이라고 한다. 모델을 **`pickle`** 할 때 **웹에서 사용**하기 위해서는 **모델을 직렬화하거나 구조를 평평하게 만든다**. 피클 파일에는 '.pkl'이라는 접미사가 있다.


### 연습 - 데이터 정제

이 자료에서는 `NUFORC(The National UFO Reporting Center)`가 수집한 **80000개의 UFO 목격 데이터**를 사용한다.

이 자료에 담겨있는 UFO 목격 데이터의 예시
- "A man emerges from a beam of light that shines on a grassy field at night and he runs towards the Texas Instruments parking lot".
- "the lights chased us".

`ufos.csv 스프레드시트`는 목격이 발생한 **도시**, **주**, **국가**, **객체의 모양**, **위도**, **경도**에 대한 열이 포함되어 있다.

*`pandas`, `matplotlib`, `numpy`를 사용하고, `ufos 스프레드시트`를 사용할 것이다.`*

```python
import pandas as pd
import numpy as np

ufos = pd.read_csv('./data/ufos.csv')
ufos.head()
```























___

## 참고 :
💥 **위의 모든 내용은 [ML-For-Beginners의 자료](https://github.com/codingalzi/ML-For-Beginners/tree/main/3-Web-App)를 참고하여 작성했습니다**

💥 추가 참고 자료 : [Flask Web Framework](https://opentutorials.org/course/4904)
