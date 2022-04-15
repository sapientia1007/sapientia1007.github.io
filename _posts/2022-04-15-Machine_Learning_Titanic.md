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
---

## Kaggle로 생존자 예측하기
 
- `Kaggle`에 있는 `Titanic 데이터`로 생존자를 **예측**하는 훈련을 하기 위해서 [Kaggle Titanic Machine Learning](https://www.kaggle.com/competitions/titanic/data)을 접속한다.
-  `Creat New Notebook` -> `Add Data` -> `titanic` 검색 -> `Competition Data` -> `Titanic- Machine Learning from Disaster` 선택 -> `Add`
-  `Kaggle 주피터 노트북`에 해당 타이타닉 데이터를 연결하여 **훈련**을 하고 해당 **Competition**에 **예측을 제출**한다.

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

위 데이터가 나타내는 의미는 다음과 같다.
- Survived: 생존 여부 -> 0 : 사망, 1 : 생존
- pclass: 객실 등급 -> Pclass가 낮은 수일수록 좋은 등급 
- Sex: 성별
- Age: 나이
- Sibsp: 함께 탑승한 형제자매, 배우자의 수
- Parch: 함께 탑승한 부모, 자식의 수
- Ticket: 티켓 번호
- Fare: 운임
- Cabin: 객실 번호
- Embarked: 탑승 장소 -> C : Cherbourg, Q : Queenstown, S  Southampton

불러온 데이터를 훈련하기 위해, 데이터들을 시각화하여 확인한다.
```python
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
sns.set()
```
```python
import warnings
warnings.filterwarnings('ignore')
f, ax = plt.subplots(1, 2, figsize=(18,8))
train['Survived'].value_counts().plot.pie(explode=[0,0.1],autopct='%1.1f%%',ax=ax[0],shadow=True)
ax[0].set_title('Survived')
ax[0].set_ylabel('')
sns.countplot('Survived',data=train,ax=ax[1])
ax[1].set_title('Survived')
plt.show()
# 탑승객의 60%가 사망한것을 확인할 수 있음(Survived의 0 : 사망/ 1 : 생존)
```
![res1](http://jjhcom.github.io/assets/images/banners/res1.png)
**생존자**와 **사망자**의 비율을 시각화한것으로, **사망자의 비율이 더 많다는 것**을 알 수 있다.


다음으로, **남녀 생존 비율**을 하며 시각화하여 확인한다.
```python
f,ax=plt.subplots(1,2,figsize=(18,8))
train['Survived'][train['Sex']=='male'].value_counts().plot.pie(explode=[0,0.1],autopct='%1.1f%%',ax=ax[0],shadow=True)
train['Survived'][train['Sex']=='female'].value_counts().plot.pie(explode=[0,0.1],autopct='%1.1f%%',ax=ax[1],shadow=True)
ax[0].set_title('Survived (male)')
ax[1].set_title('Survived (female)')
plt.show()
```
![res2](http://jjhcom.github.io/assets/images/banners/res2.png)
여성보다 **남성의 사망률이 더 크다는 것**을 확인할 수 있다.

**Pclass 등급에 따른 생존율**을 확인한다.
```python
pd.crosstab([train['Sex'],train['Survived']],train['Pclass'],margins=True)
```
```shell
Pclass	1	2	3	All
Sex	Survived				
female	0	3	6	72	81
1	91	70	72	233
male	0	77	91	300	468
1	45	17	47	109
All		216	184	491	891
```
위 결과를 통해서, **Pclass가 좋을수록 사망률이 낮다는 것**을 확인할 수 있다.

즉, 객실의 등급이 높을수록 생존한 확률이 높다는 것이다.


다음으로 **탑승 장소에 다른 생존율**을 확인한다.
```python
# (C = Cherbourg, Q = Queenstown, S = Southampton)을 의미
pd.crosstab([train['Sex'],train['Survived']],train['Embarked'],margins=True)
```
```shell
Embarked	C	Q	S	All
Sex	Survived				
female	0	9	9	63	81
1	64	27	140	231
male	0	66	38	364	468
1	29	3	77	109
All		168	77	644	889
```
위 결과로 확인할 수 있던 내용은 다음과 같다.
- Southampton에서 탑승한 사람이 많았고, 생존자보다 사망자의 비율이 컸다.
- Cherbourg에서 탑승한 사람 중에서는 생존한 사람의 비율이 가장 많았다.
- Queenstown에서 탑승한 사람도 생존자보다 사망자의 비율이 컸다.


위 모든 `데이터 분석`을 통하여, 

### 전처리 과정

---

[Kaggle에 업로드한 코드](https://www.kaggle.com/code/shimjh/titanic-assignment)
