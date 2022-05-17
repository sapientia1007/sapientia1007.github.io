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

# 데이터 불러오기
ufos = pd.read_csv('./ufos.csv')
ufos.head()
```
```
	datetime	city	state	country	shape	duration (seconds)	duration (hours/min)	comments	date posted	latitude	longitude
0	10/10/1949 20:30	san marcos	tx	us	cylinder	2700.0	45 minutes	This event took place in early fall around 194...	4/27/2004	29.883056	-97.941111
1	10/10/1949 21:00	lackland afb	tx	NaN	light	7200.0	1-2 hrs	1949 Lackland AFB&#44 TX. Lights racing acros...	12/16/2005	29.384210	-98.581082
2	10/10/1955 17:00	chester (uk/england)	NaN	gb	circle	20.0	20 seconds	Green/Orange circular disc over Chester&#44 En...	1/21/2008	53.200000	-2.916667
3	10/10/1956 21:00	edna	tx	us	circle	20.0	1/2 hour	My older brother and twin sister were leaving ...	1/17/2004	28.978333	-96.645833
4	10/10/1960 20:00	kaneohe	hi	us	light	900.0	15 minutes	AS a Marine 1st Lt. flying an FJ4B fighter/att...	1/22/2004	21.418056	-157.803611
```

`ufos 데이터`를 작은 데이터프레임으로 변환하고, `Country 필드`의 고유값을 확인할 것이다.

```python
# 특정 데이터로 데이터 프레임을 형성 후 'Country' 필드의 고유한 값 확인하기
ufos = pd.DataFrame({'Seconds': ufos['duration (seconds)'], 'Country': ufos['country'],'Latitude': ufos['latitude'],'Longitude': ufos['longitude']})

ufos.Country.unique()
```
```
array(['us', nan, 'gb', 'ca', 'au', 'de'], dtype=object)
```

누락값을 삭제하고, `Seconds`데이터에서 원하는 정보만 저장할 것이다. 
```python
# 누락값 삭제
ufos.dropna(inplace=True)

# ufos의 'Seconds' 데이터에서 1초~60초 사이의 목격 정보만 가져오기
ufos = ufos[(ufos['Seconds'] >= 1) & (ufos['Seconds'] <= 60)]

ufos.info()
```
```
<class 'pandas.core.frame.DataFrame'>
Int64Index: 25863 entries, 2 to 80330
Data columns (total 4 columns):
 #   Column     Non-Null Count  Dtype  
---  ------     --------------  -----  
 0   Seconds    25863 non-null  float64
 1   Country    25863 non-null  object 
 2   Latitude   25863 non-null  float64
 3   Longitude  25863 non-null  float64
dtypes: float64(3), object(1)
memory usage: 1010.3+ KB
```

Scikit-learn의 `LabelEncoder 라이브러리`를 가져와서 **국가의 텍스트 값**을 **숫자로 변환**한다.

```python
from sklearn.preprocessing import LabelEncoder

# 'Country' 데이터의 텍스트 값을 숫자로 변환하여 저장 
ufos['Country'] = LabelEncoder().fit_transform(ufos['Country'])

ufos.head()
```
```
	Seconds	Country	Latitude	Longitude
2	20.0	3	53.200000	-2.916667
3	20.0	4	28.978333	-96.645833
14	30.0	4	35.823889	-80.253611
23	60.0	4	45.582778	-122.352222
24	3.0	3	51.783333	-0.783333
```

### 연습 - 모델 구축 

이제 데이터를 **교육** 및 **테스트** 그룹으로 나누어 **모델을 교육**할 준비를 할 수 있다.

`X 벡터`로 훈련할 세 가지 기능을 선택하면 해당 벡터가 국가가 된다.

초, 위도, 경도를 입력하고 반환할 국가 ID를 얻으려고 한다.

```python
from sklearn.model_selection import train_test_split

# 데이터 선별 
Selected_features = ['Seconds','Latitude','Longitude']

X = ufos[Selected_features]
y = ufos['Country']

# 교육 및 테스트 셋으로 분리
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
```

**로지스틱 회귀**를 사용하여 **모델을 훈련**할 것이다.

```python
from sklearn.metrics import accuracy_score, classification_report
from sklearn.linear_model import LogisticRegression

# 로지스틱 회귀로 정확도 예측 -> 약 97%
model = LogisticRegression(max_iter=1000) # max_iter을 설정하지 않으면 오류가 발생하여 max_iter을 설정했다.
model.fit(X_train, y_train)
predictions = model.predict(X_test)

print(classification_report(y_test, predictions))
print('Predicted labels: ', predictions)
print('Accuracy: ', accuracy_score(y_test, predictions))
```
```
              precision    recall  f1-score   support

           0       1.00      1.00      1.00        41
           1       0.85      0.47      0.60       250
           2       1.00      1.00      1.00         8
           3       1.00      1.00      1.00       131
           4       0.97      1.00      0.98      4743

    accuracy                           0.97      5173
   macro avg       0.96      0.89      0.92      5173
weighted avg       0.97      0.97      0.97      5173

Predicted labels:  [4 4 4 ... 3 4 4]
Accuracy:  0.9702300405953992
```

**국가**와 **위도/경도**의 `상관관계`가 **정확도**이기 때문에, **정확도**는 약 **97%** 로 나쁘지 않은 결과이다. 

생성한 모델은 *위도 및 경도에서 국가를 추론할 수 있을 만큼* 혁명적인 것은 아니지만, 정제하고 추출한 **원래 데이터**로부터 훈련하고 웹 앱에서 모델을 구축하는 것은 좋은 연습이다.


### 연습 - 모델 Pickle

모델에 **'Pickle'** 을 할 것이다.

피클이 완료되면, **피클 모델**을 로드하여 **초, 위도, 경도의 값을 포함**한 **샘플 데이터 배열**과 비교하여 테스트한다.

```python
# Pickle 과정 : Pickle -> .pkl의 확장자를 가진 피클 모델 로드 -> 테스트
import pickle
model_filename = 'ufo-model.pkl'
pickle.dump(model, open(model_filename,'wb'))

model = pickle.load(open('ufo-model.pkl','rb'))
print(model.predict([[50,44,-12]]))

# 이 과정에서 UserWarning에 발생하는데, X가 feature 이름을 데이터 프레임이지만, feature 이름 없이 값만 사용하면 경고가 발생하지 않는다. 하지만 '경고'이므로 넘어가보도록 한다. 
```
```
[3]
C:\Users\user\anaconda3\lib\site-packages\sklearn\base.py:451: UserWarning: X does not have valid feature names, but LogisticRegression was fitted with feature names
  "X does not have valid feature names, but"
```
이 모델의 결과가 **3**을 반환했다. 이 **3**은 <u>영국의 국가 코드</u>이다.






















___

## 참고 :
💥 **위의 모든 내용은 [ML-For-Beginners의 자료](https://github.com/codingalzi/ML-For-Beginners/tree/main/3-Web-App)를 참고하여 작성했습니다**

💥 추가 참고 자료 : [Flask Web Framework](https://opentutorials.org/course/4904)
