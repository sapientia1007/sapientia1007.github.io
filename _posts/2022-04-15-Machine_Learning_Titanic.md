---
layout: post
title: Machine Learning - Titanic
subheading: Titanic Predictions
banner:
  loop: true
  volume: 0.0
  start_at: 8.5
  image: http://jjhcom.github.io/assets/images/banners/titanic_img.jpg
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
categories: Machine_Learning
tags: Machine_Learning
use_math: true
sidebar: []
---

## Kaggle로 생존자 예측하기 (머신러닝)


 
`Kaggle`에 있는 `Titanic 데이터`로 생존자를 **예측**하는 훈련을 하기 위해서 [Kaggle Titanic Machine Learning](https://www.kaggle.com/competitions/titanic/data)을 접속하여 `Creat New Notebook` -> `Add Data` -> `titanic` 검색 -> `Competition Data` -> `Titanic- Machine Learning from Disaster` 선택 -> `Add`로 `Kaggle 주피터 노트북`에 해당 타이타닉 데이터를 연결하여 **훈련**을 하고 해당 **Competition**에 **예측을 제출**한다.

---
### 데이터 읽기 및 분석

해당 Kaggle 주피터 노트북에 있는 데이터를 불러온다.

```python
import numpy as np 
import pandas as pd 
import os

train = pd.read_csv("/kaggle/input/titanic/train.csv")
test = pd.read_csv("/kaggle/input/titanic/test.csv")

# train 데이터의 앞부분을 확인
train.head()
```
```shell
PassengerId	Survived	Pclass	Name	Sex	Age	SibSp	Parch	Ticket	Fare	Cabin	Embarked
0	1	0	3	Braund, Mr. Owen Harris	male	22.0	1	0	A/5 21171	7.2500	NaN	S
1	2	1	1	Cumings, Mrs. John Bradley (Florence Briggs Th...	female	38.0	1	0	PC 17599	71.2833	C85	C
2	3	1	3	Heikkinen, Miss. Laina	female	26.0	0	0	STON/O2. 3101282	7.9250	NaN	S
3	4	1	1	Futrelle, Mrs. Jacques Heath (Lily May Peel)	female	35.0	1	0	113803	53.1000	C123	S
4	5	0	3	Allen, Mr. William Henry	male	35.0	0	0	373450	8.0500	NaN	S
```

---

[Kaggle에 업로드한 코드](https://www.kaggle.com/code/shimjh/titanic-assignment)
