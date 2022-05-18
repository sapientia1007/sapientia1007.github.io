---
layout: post
title: 머신러닝 모델을 사용하여 Web App 생성하기
subtitle: Machine_Learning_Assignment9
categories: Machine_Learning
tags: Machine_Learning
use_math: true
---

### 학습하기

📕 이 섹션에서는 ML 항목에 적용된 `Scikit-learn 모델`을 웹 응용 프로그램 내에서 예측하는 데 사용할 수 있는 파일로 저장하는 방법을 알아볼 것이다. 
  - 모델이 저장되면 **Flask**에 내장된 **웹 앱**에서 모델을 사용하는 방법을 배울 수 있다.

📕 이 섹션에서 학습할 내용은 다음과 같다.
- **훈련 모델**을 **"pickle"** 하는 방법을 알아볼 것이다.
- **Flask App**에서 **모델을 사용**하는 방법을 알아볼 것이다.

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

  > 이 자료에 담겨있는 UFO 목격 데이터의 예시
  >
  > - "A man emerges from a beam of light that shines on a grassy field at night and he runs towards the Texas Instruments parking lot".
  >
  > - "the lights chased us".

`ufos.csv 스프레드시트`는 목격이 발생한 **도시**, **주**, **국가**, **객체의 모양**, **위도**, **경도**에 대한 열이 포함되어 있다.

`pandas`, `matplotlib`, `numpy`를 사용하고, `ufos 스프레드시트`를 사용할 것이다.

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

`ufos 데이터`를 **작은 데이터프레임으로 변환**하고, `Country 필드`의 **고유값**을 확인할 것이다.

```python
# 특정 데이터로 데이터 프레임을 형성 후 'Country' 필드의 고유한 값 확인하기
ufos = pd.DataFrame({'Seconds': ufos['duration (seconds)'], 'Country': ufos['country'],'Latitude': ufos['latitude'],'Longitude': ufos['longitude']})

ufos.Country.unique()
```
```
array(['us', nan, 'gb', 'ca', 'au', 'de'], dtype=object)
```

**누락값**을 삭제하고, `Seconds`데이터에서 **원하는 정보만 추출하여 저장**할 것이다. 
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

`X 벡터`로 <u>훈련할 세 가지 기능</u>을 선택하면 **해당 벡터**가 `국가`가 된다.

*초, 위도, 경도를 입력*하고 **반환할 국가 ID**를 얻으려고 한다.

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
model = LogisticRegression(max_iter=1000) # max_iter을 설정하지 않으면 ConvergenceWarning이 발생하여 max_iter=1000을 설정했다.
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

# 이 과정에서 UserWarning에 발생하는데, 이는 데이터 프레임에 있는 데이터를 사용하여 모델을 적합시키고, 값만 사용하여 예측하기 때문에 나타난다고 예측할 수 있다.
# 하지만 오류가 아닌 '경고'이므로 일단 넘어가보도록 한다. 
```
```
[3]
C:\Users\user\anaconda3\lib\site-packages\sklearn\base.py:451: UserWarning: X does not have valid feature names, but LogisticRegression was fitted with feature names
  "X does not have valid feature names, but"
```
이 모델의 결과가 **3**을 반환했다. 이 **3**은 <u>영국의 국가 코드</u>이다.

### 연습 - Flask app 구축

모델을 호출하기 위해 **Flask app**을 구축하고 비슷한 결과를 반환할 수 있지만, 더 시각적으로 만족스러운 방식이다. 

1. `ufo-model.pkl` 파일과 `notebook.ipynb`이 있는 곳에 `web-app`이라 불리는 폴더를 생성한다.
![res_1](http://jjhcom.github.io/assets/images/banners/ml_assignment1.jpg)
2. 방금 생성한 `web-app` 폴더에 `css`폴더를 가진 `static`폴더를 생성하고, `templates`폴더 생성한다.
![res_2](http://jjhcom.github.io/assets/images/banners/ml_assignment2.jpg)
![res_3](http://jjhcom.github.io/assets/images/banners/ml_assignment3.jpg)
3. `web-app` 폴더에 `requirements.txt`파일을 만든다. *자바스크립트 앱의 패키지처럼, 이 파일은 앱에 필요한 의존성을 나열한다.*
![res_4](http://jjhcom.github.io/assets/images/banners/ml_assignment4.jpg)
- `requirements.txt`파일에 아래와 같이 작성한다.
```
scikit-learn
pandas
numpy
flask
```
4. 터미널에서 `web-app`으로 이동해서 파일을 동작시킨다.
5. 터미널에서 `pip install` 명령어로 `requirements.txt`에 있는 라이브러리를 설치한다.
```
pip install -r requirements.txt
```
![res_5](http://jjhcom.github.io/assets/images/banners/ml_assignment5.jpg)
6. 앱을 완성하기 위해 3개의 더 많은 파일을 만들 것이다.
  - 이 경로에 `app.py`를 형성한다.
  - `templates` 디렉터리에 `index.html`을 형성한다.
  - `static/css` 디렉터리에 `style.css`를 형성한다.
![res_10](http://jjhcom.github.io/assets/images/banners/ml_assignment10.jpg)
![res_11](http://jjhcom.github.io/assets/images/banners/ml_assignment11.jpg)
![res_12](http://jjhcom.github.io/assets/images/banners/ml_assignment12.jpg)
7. `style.css`파일에 아래와 같이 작성한다.
```
body {
	width: 100%;
	height: 100%;
	font-family: 'Helvetica';
	background: black;
	color: #fff;
	text-align: center;
	letter-spacing: 1.4px;
	font-size: 30px;
}

input {
	min-width: 150px;
}

.grid {
	width: 300px;
	border: 1px solid #2d2d2d;
	display: grid;
	justify-content: center;
	margin: 20px auto;
}

.box {
	color: #fff;
	background: #2d2d2d;
	padding: 12px;
	display: inline-block;
}
```
8. `index.html`파일에 아래와 같이 작성한다.
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>🛸 UFO 목격 예측하기! 👽</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/styles.css') }}">
  </head>

  <body>
    <div class="grid">

      <div class="box">

        <p>'초의 수', '위도', '경도'에 따라 어느 나라가 UFO를 목격했다고 보고했을까?</p>

        <form action="{{ url_for('predict')}}" method="post">
          <input type="number" name="seconds" placeholder="Seconds" required="required" min="0" max="60" />
          <input type="text" name="latitude" placeholder="Latitude" required="required" />
          <input type="text" name="longitude" placeholder="Longitude" required="required" />
          <button type="submit" class="btn"> UFO 목격 나라를 예측 </button>
        </form>

        <p>{{ prediction_text }}</p>

      </div>

    </div>

  </body>
</html>
```

9. 모델의 소비와 예측 표시를 주도하는 python 파일을 구축하기 위해 `app.py`에 추가한다.
```python
import numpy as np
from flask import Flask, request, render_template
import pickle

app = Flask(__name__)

model = pickle.load(open("../ufo-model.pkl", "rb"))


@app.route("/")
def home():
    return render_template("index.html")


@app.route("/predict", methods=["POST"])
def predict():

    int_features = [int(x) for x in request.form.values()]
    final_features = [np.array(int_features)]
    prediction = model.predict(final_features)

    output = prediction[0]

    countries = ["Australia", "Canada", "Germany", "UK", "US"]

    return render_template(
        "index.html", prediction_text="예측 국가: {}".format(countries[output])
    )


if __name__ == "__main__":
    app.run(debug=True)
```
> 📌  **Flask**를 사용하는 웹앱을 동작하는 동안 'debug=True'를 추가할 때, 서버의 재시작을 필요로 하는 것 없이 응용프로그램에 대한 변경 사항이 즉시 반영된다. 즉, 프로덕션 앱에서 이 모드를 활성화하지 않는 것을 추천한다.

만약 python(python3) 파일 `app.py`를 동작시킨다면, 웹 서버는 로컬에서 시작되고 UFO가 목격된 위치에 대해 궁금증의 답을 얻는 짧은 양식을 작성할 수 있다. 

`app.py` 살펴보기
1. 첫번째, 먼저 종속성이 로드되고 앱이 시작된다.
2. 모델을 가져온다.
3. index.html이 홈 경로에 렌더링한다.

![res_6](http://jjhcom.github.io/assets/images/banners/ml_assignment6.jpg)

`/predict 경로`에 양식이 게시될 때 다음과 같은 몇 가지 일이 발생한다.
1. 폼 변수가 수집되고 numpy 배열로 변환된다. 그런 다음 이러한 정보가 모델로 전송되고 예측이 반환된다.
2. 표시할 국가는 예측 국가 코드에서 읽을 수 있는 텍스트로 다시 렌더링되며, 이 값은 template에 렌더링되도록 index.html로 전송된다.

만들어진 웹 앱의 결과는 다음와 같다.
![res_7](http://jjhcom.github.io/assets/images/banners/ml_assignment7.jpg)
이전에 코드로 예측했던 정보들을 이용하여 같은 결과가 나오는지 확인해볼 것이다.
이전에 'Seconds = 50 / Latitude = 44 / Longitude = -12'를 입력했을 때 영국의 국가 코드 '3'을 반환했다.
![res_8](http://jjhcom.github.io/assets/images/banners/ml_assignment8.jpg)
![res_9](http://jjhcom.github.io/assets/images/banners/ml_assignment9.jpg)
이 웹앱에서는 UK를 반환하는데, 똑같이 영국을 예측한다.

Falsk와 Pickled 모델을 사용하여 이러한 방식으로 모델을 사용하는 것은 비교적 간단하다.

가장 어려운 것은 예측하기 위해 모델로 전송되어야 하는 데이터가 어떤 형태인지 이해하는 것이다.

그것은 어떻게 그 모델이 훈련되는지에 따라 달려 있다.

예측을 얻기 위해 세 개의 데이터 포이트가 입력되어야 한다.

전문적인 환경에서, 모델을 교육하는 사람과 웹이나 모바일 앱에서 모델을 소비하는 사람 사이에 얼마나 좋은 의사소통이 중요한지 알 수 있다.



### 도전해보기
🎈 *노트북을 사용하여* **모델**을 `Flask app`으로 가져오는 대신, `Flask app` 내에서 **모델을 교육**할 수 있다.

🎈 데이터가 정리된 후, 노트북에서 **Python 코드를 변환**하여 `train`이라는 경로를 통해 **앱 내에서 모델을 교육**해본다.

🎈 또한, 이 방법을 추구하는 장단점이 무엇일지 생각해본다.



















___

## 참고 :
💥 **위의 모든 내용은 [ML-For-Beginners의 자료](https://github.com/codingalzi/ML-For-Beginners/tree/main/3-Web-App)를 참고하여 작성했습니다**

💥 추가 참고 자료 : [Flask Web Framework](https://opentutorials.org/course/4904)
