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
  height: "75vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
categories: Machine_Learning
tags: Machine_Learning
use_math: true
---

## Kaggle로 Titanic 생존자 예측

- `Kaggle`에 있는 `Titanic 데이터`로 생존자를 **예측**하는 훈련을 하기 위해서 [Kaggle Titanic Machine Learning](https://www.kaggle.com/competitions/titanic/data)을 접속한다.
-  `Creat New Notebook`→`Add Data`→`titanic` 검색→`Competition Data`→`Titanic- Machine Learning from Disaster` 선택→`Add`로 데이터를 불러온다.
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
	PassengerId	Survived    Pclass	  Name                            Sex       Age     SibSp     Parch       Ticket            Fare      Cabin     Embarked
0	    1	          0	       3	  Braund, Mr. Owen Harris         male        22.0      1         0       A/5 21171           7.2500      NaN       S
1	    2	          1	       1	  Cumings, Mrs. John Bradley      female      38.0      1         0       PC 17599            71.2833     C85       C
2	    3	          1	       3	  Heikkinen, Miss. Laina          female      26.0      0         0       STON/O2. 3101282    7.9250      NaN       S
3	    4	          1	       1	  Futrelle, Mrs. Jacques Heath    female      35.0      1         0       113803              53.1000     C123      S
4	    5	          0	       3	  Allen, Mr. William Henry        male        35.0      0         0       373450              8.0500      NaN       S
```

위 데이터가 나타내는 의미는 다음과 같다.
- **Survived** : 생존 여부 → 0 : 사망, 1 : 생존
- **pclass** : 객실 등급 → Pclass가 낮은 수일수록 좋은 등급 
- **Sex** : 성별
- **Age** : 나이
- **Sibsp** : 함께 탑승한 형제자매, 배우자의 수
- **Parch** : 함께 탑승한 부모, 자식의 수
- **Ticket** : 티켓 번호
- **Fare** : 운임
- **Cabin** : 객실 번호
- **Embarked** : 탑승 장소 → C : Cherbourg, Q : Queenstown, S  Southampton


불러온 데이터를 **훈련**하기 위해, 데이터들을 `시각화하여 확인`한다.
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

다음으로, **Pclass 등급에 따른 생존율**을 확인한다.
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
All	        216     184	491	891
```
위 결과를 통해서, **Pclass가 좋을수록 사망률이 낮다는 것**을 확인할 수 있다.

즉, 객실의 등급이 높을수록 생존한 확률이 높다는 것이다.


다음으로 **탑승 장소에 따른 생존율**을 확인한다.
```python
# (C = Cherbourg, Q = Queenstown, S = Southampton)을 의미
pd.crosstab([train['Sex'],train['Survived']],train['Embarked'],margins=True)
```
```shell
        Embarked    C   Q   S   All
Sex     Survived
female      0       9   9   63    81
            1       64  27  140   231
male        0       66  38  364   468
            1       29  3   77    109
All                 168 77  644   889
```
위 결과로 확인할 수 있던 내용은 다음과 같다.
- Southampton에서 탑승한 사람이 많았고, 생존자보다 사망자의 비율이 컸다.
- Cherbourg에서 탑승한 사람 중에서는 생존한 사람의 비율이 가장 많았다.
- Queenstown에서 탑승한 사람도 생존자보다 사망자의 비율이 컸다.


위에서 **분석한 데이터**를 통하여, 생존자를 예측할 것이다.

---

### 전처리 과정

전처리 과정을 위해서, 먼저 **결측값**이 있는지 확인한다.
```python
train.isnull().sum()
```
```shell
PassengerId      0
Survived         0
Pclass           0
Name             0
Sex              0
Age            177
SibSp            0
Parch            0
Ticket           0
Fare             0
Cabin          687
Embarked         2
dtype: int64
```
위의 결과를 통해 `Age`, `Cabin`, `Embarked`에 **결측값**이 있다는 것을 확인할 수 있다.


*성능 좋은 데이터 훈련*을 위해, **결측값을 처리**하는 `전처리 과정`이 필요하다.

**전처리 과정**에도 많은 방법이 있지만, 이번에는 사용할 데이터의 결측값만 채우고 넘어갈 것이다.

즉, 우리가 사용할 데이터인 `Embarked`의 결측값은, 사람들이 **가장 많이 탑승한 `Southampton`으로 가정하여 채운다.**

```python
# 'Embarked'의 누락값을 제일 많이 탑승한 항구인 'Southampton'에서 탔다고 가정하여 누락값을 채운다
train['Embarked'].fillna('S',inplace=True) 
```

이전에 분석했던 데이터들을 이용하여 예측할 것이므로, 이제 **예측 모델**을 생성할 것이다.


---

### 예측 모델 생성 및 결과

먼저, **변환기**들을 이용해 `수치형 데이터`들을 **전처리하기 위한 파이프라인**을 형성한다.

```python
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn.neighbors import KNeighborsClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.naive_bayes import GaussianNB
from sklearn.utils import shuffle
```
```python
# 수치 속성을 위한 파이프라인부터 시작하여 전처리 파이프라인을 구축
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler

num_pipeline = Pipeline([
        ("imputer", SimpleImputer(strategy="median")),
        ("scaler", StandardScaler())
    ])
 ```
 ```python
 # 범주 속성을 위한 파이프라인을 구축
from sklearn.preprocessing import OrdinalEncoder, OneHotEncoder
cat_pipeline = Pipeline([
        ("ordinal_encoder", OrdinalEncoder()),    
        ("imputer", SimpleImputer(strategy="most_frequent")),
        ("cat_encoder", OneHotEncoder(sparse=False)),
    ])
```
```python
# 수치 및 범주형 파이프라인
from sklearn.compose import ColumnTransformer

num_attribs = ["Age", "SibSp", "Parch", "Fare"]
cat_attribs = ["Pclass", "Sex", "Embarked"]

preprocess_pipeline = ColumnTransformer([
        ("num", num_pipeline, num_attribs),
        ("cat", cat_pipeline, cat_attribs),
    ])
```

`머신러닝 모델`에 제공할 수 있는 *수치 입력 기능을 출력*하는 **전처리 파이프라인**이 생성되었다.

```python
# 데이터를 변환
X_train = preprocess_pipeline.fit_transform(train)
X_train
```
```shell
array([[-0.56573646,  0.43279337, -0.47367361, ...,  0.        ,
         0.        ,  1.        ],
       [ 0.66386103,  0.43279337, -0.47367361, ...,  1.        ,
         0.        ,  0.        ],
       [-0.25833709, -0.4745452 , -0.47367361, ...,  0.        ,
         0.        ,  1.        ],
       ...,
       [-0.1046374 ,  0.43279337,  2.00893337, ...,  0.        ,
         0.        ,  1.        ],
       [-0.25833709, -0.4745452 , -0.47367361, ...,  1.        ,
         0.        ,  0.        ],
       [ 0.20276197, -0.4745452 , -0.47367361, ...,  0.        ,
         1.        ,  0.        ]])
```
```python
# 생존 여부 확인
y_train = train["Survived"]
y_train
```
```shell
0      0
1      1
2      1
3      1
4      0
      ..
886    0
887    1
888    0
889    1
890    0
Name: Survived, Length: 891, dtype: int64
```


위에서 변환한 데이터들을 **훈련**을 돌릴 것 이다.

먼저, `RandomFroestClassifer`로 훈련을 돌려본다.
```python
# RandomForestClassifer로 훈련
from sklearn.ensemble import RandomForestClassifier
forest_clf = RandomForestClassifier(n_estimators=100, random_state=42)
forest_clf.fit(X_train, y_train)
```

위 **교육받은 모델**로 `테스트셋에 대한 예측`을 구할것이다.
```python
# 교육받은 모델로 테스트 셋에 대한 예측
X_test = preprocess_pipeline.transform(test)
y_pred = forest_clf.predict(X_test)
```
```python
from sklearn.model_selection import cross_val_score
forest_scores = cross_val_score(forest_clf, X_train, y_train, cv=10)
forest_scores.mean()
```
```shell
0.8092759051186016
```
`RandomForestClassifier`로 돌렸을 때 약 **80%의 성능**이 나온다.

다음으로, `SVC`로 훈련을 돌려본다.
```python
from sklearn.svm import SVC
svm_clf = SVC(gamma="auto")
svm_scores = cross_val_score(svm_clf, X_train, y_train, cv=10)
svm_scores.mean()
```
```shell
0.8249313358302123
```
`SVC`로 돌렸을 때 약 **82%의 성능**이 나온다.

이는 `RandomForestClassifier`보다 **성능이 좋다는 것을 확인**할 수 있다.

즉, 성능이 더 좋은 `SVC`로 **테스트 셋에 대한 예측**을 수행할 것이다.
```python
svm_clf.fit(X_train, y_train)
X_test = preprocess_pipeline.transform(test)
y_pred = svm_clf.predict(X_test)
```

위 과정들로 얻은 데이터들의 필요한 값들로 **데이터프레임**을 형성하고, `csv 파일`로 형성한다.

```python
submission = pd.DataFrame({"PassengerId": test["PassengerId"],"Survived": y_pred })

submission.to_csv('submission.csv', index=False)
submission
```
```shell

  PassengerId	Survived
0	892	0
1	893	1
2	894	0
3	895	0
4	896	1
...	...	...
413	1305	0
414	1306	1
415	1307	0
416	1308	0
417	1309	0
418 rows × 2 columns
```

이렇게 만들어진 `csv 파일`을 캐글 사이트에 제출하여, **정확도에 대한 점수**를 확인한다.


---

### 결과
![rank1](http://jjhcom.github.io/assets/images/banners/rank1.jpg)
위 결과는 처음에, `RandomForestClassifier`로 훈련했을 때 나온 등수이다.


아래는 `SVC`로 훈련했을 때 나온 등수이다. 
![rank3](http://jjhcom.github.io/assets/images/banners/rank3.jpg)

`RandomForestClassifier`보다 `SVC`로 훈련했을 때 성능이 더 좋아 성적이 좋았다는 것을 확인할 수 있다.

**데이터 분석**, **전처리**, **훈련** 등등 여러 과정이 아직 익숙하지 않아, 아직 코드가 깔끔하지 않아서 아쉽다는 생각이 든다.

하지만, 이번에 직접 해보면서 *앞으로 어떻게 훈련과정을 해야할지* 다시 생각할 수 있었고, 앞으로 더 좋은 성능을 확인하기 위해 **더 많은 훈련 연습이 필요할 것 같다는 것**을 느꼈다.

처음으로 도전해본 `Titanic Machine Learning 예측` 과정이므로, 아쉽지만 그래도 많은 것을 알 수 있었기 때문에 만족한다.



---

[Kaggle에 업로드한 코드](https://www.kaggle.com/code/shimjh/titanic-assignment)
