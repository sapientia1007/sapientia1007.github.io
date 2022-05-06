---
layout: post
title: Machine_Learning_For_Beginners
subtitle: Machine_Learning_Assignment7
categories: Machine_Learning
tags: Machine_Learning
use_math: true
---

(계속 작성중)

## Introduction
고전적인 머신러닝의 기본적인 초점 **분류**에 대해 알아볼 것이다.

*아시아*와 *인도*의 *모든 훌륭한 요리에 대한 **데이터 세트***를 사용하여 `다양한 분류 알고리즘`을 사용할 것이다.

### 분류
- **분류**는 **회귀 기술과 많은 공통점**을 가진 `지도 학습의 한 형태`이다.
- 만약 *머신러닝이 데이터셋을 사용하여 값이나 이름을 예측하는 것*이라면, **분류**는 일반적으로 `이진 분류`와 `다중 클래스 분류`의 두 그룹으로 나뉜다.
   - [분류 참고 영상](https://www.youtube.com/watch?v=eg8DJYwdMyg&feature=youtu.be)

📌 **기억**
- **선형 회귀 분석**을 사용하면 *변수 간의 관계를 예측*하고 *해당 선과 관련하여 새 데이터 점이 포함될 위치를 정확하게 예측*할 수 있다.
- **로지스틱 회귀 분석**을 사용하면 "이진 범주"를 발견할 수 있다.


**분류**는 *데이터 포인트의 레이블이나 클래스를 결정하기 위해* 다양한 알고리즘을 사용한다.

이 **요리 데이터**를 사용하여 재료 그룹을 관찰함으로써 `음식의 유래를 결정`할 수 있는지 알아보는 과정을 볼 것이다.

**분류**는 `기계 학습 연구자`와 `데이터 과학자`의 기본 활동 중 하나이다.

*"이 메일은 스팸인지 아닌지"를 분류*하는 것과 같은 **이진 값의 기본 분류**에서 컴퓨터 비전을 사용한 복잡한 이미지 분류 및 분할에 이르기까지 데이터를 클래스로 정렬하고 그에 대한 질문을 할 수 있으면 항상 유용하다.

보다 과학적인 방법으로 공정을 설명하기 위해, **분류 방법**에서는 입력 *변수와 출력 변수 사이의 관계를 매핑할 수 있는 **예측 모형***을 만든다.

데이터를 정리하고 시각화하고 ML 작업에 대비하는 프로세스를 시작하기 전에, 머신 러닝을 활용하여 `데이터를 분류하는 다양한 방법`에 대해 알아볼 것이다.
- 통계에서 파생된 고전적 머신 러닝을 사용한 분류는 '흡연자, 체중, 나이'와 같은 특징을 사용하여 'X 질환의 발병 가능성'을 결정한다.
- 앞서 수행한 회귀 연습과 유사한 지도학습 기법으로, 데이터에 레이블이 지정되고 ML 알고리즘은 이러한 레이블을 사용하여 데이터 셋의 클래스 또는 기능을 분류하고 예측하여 그룹 또는 결과에 할당한다.


요리에 대한 데이터셋을 상상할때 생각해야 할 점
- 다중 클래스 모델이 대답할 수 있는 것은 무엇?
- 이진 모델이 대답할 수 있는 것은 무엇?
- 주어진 요리가 페누그릭을 사용할 가능성이 있는지 결정하기 위해 무엇?
- 스타 아니스, 아티초크, 콜리플라워, 고추냉이가 가득한 식료품 봉지의 선물로 전형적인 인도 요리를 만들 수 있는지를 확인하기 위해 무엇?

우리는 몇 가지 잠재적 국가 요리를 다룰 수 있기 때문에, 우리가 요리 데이터 셋에 대해 묻고 싶은 질문은 사실 **다중 클래스 질문**이다.

성분 배치가 주어졌을 때, 이 많은 등급 중 어떤 데이터가 적합할지 생각해봐야 한다.

`Scikit-learn`은 해결하려는 *문제의 종류*에 따라 **데이터를 분류**하는데 사용할 수 있는 몇 가지의 다른 알고리즘들을 제공한다.


### 연습
이 프로젝트를 시작하기 전에 가장 먼저 해야 할 일은 **데이터를 정리**하고 **균형을 맞춰 더 나은 결과는 얻는 것**이다.

가장 먼저 설치해야 할것은 **데이터의 균형을 더 잘 조정**할 수 있도록 지원하는 `Scikit-learn`의 패키지인 `imblearn`이다.

```python
# imblearn 설치하기 위해 pip install

pip install imblearn
```

```python
# 데이터를 가져오는 데 필요한 패키지를 가져오고 시각화할 수 있으며, imblearn에서 SMOT도 가져올 수 있다.
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib as mpl
import numpy as np
from imblearn.over_sampling import SMOTE
```
```python
# 데이터 가져와서 읽기
# read_csv()를 사용하여 해당 csv파일의 내용을 읽고 변수 df에 저장
df  = pd.read_csv('../data/cuisines.csv')
# 앞에서부터 5개의 데이터를 확인
df.head()
```

|     | Unnamed: 0 | cuisine | almond | angelica | anise | anise_seed | apple | apple_brandy | apricot | armagnac | ... | whiskey | white_bread | white_wine | whole_grain_wheat_flour | wine | wood | yam | yeast | yogurt | zucchini | 
| --- | ---------- | ------- | ------ | -------- | ----- | ---------- | ----- | ------------ | ------- | -------- | --- | ------- | ----------- | ---------- | ----------------------- | ---- | ---- | --- | ----- | ------ | -------- | 
| 0   | 65         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        | 
| 1   | 66         | indian  | 1      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        | 
| 2   | 67         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        | 
| 3   | 68         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        | 
| 4   | 69         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 1      | 0        | 

5 rows × 385 columns
```python
# info()를 호출하여 데이터에 대한 정보를 가져오기
df.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 2448 entries, 0 to 2447
Columns: 385 entries, Unnamed: 0 to zucchini
dtypes: int64(384), object(1)
memory usage: 7.2+ MB
```

**요리 당 데이터의 분포를 알아보기**

```python
# barh() 호출하여 데이터를 막대 그래프로 출력
df.cuisine.value_counts().plot.barh() # 데이터 분포가 고르지 않음 
```


![res_1](http://jjhcom.github.io/assets/images/banners/res_1.png)

```python
# 요리 당 얼마나 많은 데이터 사용할 수 있는지 확인
thai_df = df[(df.cuisine == "thai")]
japanese_df = df[(df.cuisine == "japanese")]
chinese_df = df[(df.cuisine == "chinese")]
indian_df = df[(df.cuisine == "indian")]
korean_df = df[(df.cuisine == "korean")]

print(f'thai df: {thai_df.shape}')
print(f'japanese df: {japanese_df.shape}')
print(f'chinese df: {chinese_df.shape}')
print(f'indian df: {indian_df.shape}')
print(f'korean df: {korean_df.shape}')
```
```
thai df: (289, 385)
japanese df: (320, 385)
chinese df: (442, 385)
indian df: (598, 385)
korean df: (799, 385)
```

이제 데이터를 더 깊이 파고들어 요리 당 전형적인 재료가 무엇인지 배울 수 있다.

음식 사이에 혼란을 일으키는 반복적인 데이터를 지워야 하는데, 이 문제에 대해 알아볼 것이다.

파이썬에서 `creat_ingredient()` 함수를 만들어 **성분 데이터 프레임을 생성**한다.

이 기능은 **도움이 되지 않는 열을 삭제**하는 것부터 시작하여 **성분을 개수에 따라 정렬**한다.

```python
def create_ingredient_df(df):
    ingredient_df = df.T.drop(['cuisine','Unnamed: 0']).sum(axis=1).to_frame('value')
    ingredient_df = ingredient_df[(ingredient_df.T != 0).any()]
    ingredient_df = ingredient_df.sort_values(by='value', ascending=False, inplace=False)
    return ingredient_df
```

```python
# 요리별로 가장 인기 있는 10대 식재료에 대한 아이디어 얻기(Thai)
thai_ingredient_df = create_ingredient_df(thai_df)
thai_ingredient_df.head(10).plot.barh()
```


![res_2](http://jjhcom.github.io/assets/images/banners/res_2.png)

```python
# 요리별로 가장 인기 있는 10대 식재료에 대한 아이디어 얻기(Japanese)
japanese_ingredient_df = create_ingredient_df(japanese_df)
japanese_ingredient_df.head(10).plot.barh()
```

![res_3](http://jjhcom.github.io/assets/images/banners/res_3.png)

```python
# 요리별로 가장 인기 있는 10대 식재료에 대한 아이디어 얻기(Chinese)
chinese_ingredient_df = create_ingredient_df(chinese_df)
chinese_ingredient_df.head(10).plot.barh()
```

![res_4](http://jjhcom.github.io/assets/images/banners/res_4.png)


```python
# 요리별로 가장 인기 있는 10대 식재료에 대한 아이디어 얻기(Indian)
indian_ingredient_df = create_ingredient_df(indian_df)
indian_ingredient_df.head(10).plot.barh()
```

![res_5](http://jjhcom.github.io/assets/images/banners/res_5.png)


```python
# 요리별로 가장 인기 있는 10대 식재료에 대한 아이디어 얻기(Korean)
korean_ingredient_df = create_ingredient_df(korean_df)
korean_ingredient_df.head(10).plot.barh()
```

![res_6](http://jjhcom.github.io/assets/images/banners/res_6.png)



```python
# drop()을 호출하여 구별되는 요리 사이에 혼란을 일으키는 가장 일반적인 재료 삭제 -> 'rice', 'garlic', 'ginger'
feature_df= df.drop(['cuisine','Unnamed: 0','rice','garlic','ginger'], axis=1)
labels_df = df.cuisine #.unique()
feature_df.head()
```


|    | almond | angelica | anise | anise_seed | apple | apple_brandy | apricot | armagnac | artemisia | artichoke | ... | whiskey | white_bread | white_wine | whole_grain_wheat_flour | wine | wood | yam | yeast | yogurt | zucchini |
| --- | ---------- | ------- | ------ | -------- | ----- | ---------- | ----- | ------------ | ------- | -------- | --- | ------- | ----------- | ---------- | ----------------------- | ---- | ---- | --- | ----- | ------ | -------- |
| 0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	... |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |
| 1 |	1 | 	0 | 	0 |	0 |	0 |	0 | 	0 |	0 |	0 |	0 |	... |	0 |	0 |	0 |	0 |	0 | 	0 |	0 |	0 |	0 |	0 |
| 2 |	0 |	0 |	0 |	0 | 	0 |	0 |	0 |	0 |	0 |	0 |	... |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 | 
| 3 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	... |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |
| 4 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	... |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	0 |	1 |	0 |

5 rows × 380 columns

이제 데이터를 정리했으므로 `SMOTE`, 즉 "Synthetic Minority Over-sampling Technique(합성 소수 과표본 기법)"을 사용하여 **균형**을 잡을 것이다.

```python
# fit_resample()을 호출하면 이 전략은 보간을 통해 새 샘플을 생성
oversample = SMOTE()
transformed_feature_df, transformed_label_df = oversample.fit_resample(feature_df, labels_df)

# 성분 당 레이블 수 확인
print(f'new label count: {transformed_label_df.value_counts()}')
print(f'old label count: {df.cuisine.value_counts()}')
```
**데이터의 균형을 유지**함으로써 *데이터를 분류할 때 더 나은 결과*를 얻을 수 있다.

**이진분류기**를 생각해보면, 대부분의 **데이터가 `하나`의 클래스**인 경우 *ML 모델은 단지 더 많은 데이터가 있다*는 이유로 **해당 클래스를 더 자주 예측**한다.

**데이터의 균형**을 맞추려면 왜곡된 데이터가 필요하며 이러한 **불균형을 제거**하는 데 도움이 된다.

```
new label count: thai        799
indian      799
japanese    799
chinese     799
korean      799
Name: cuisine, dtype: int64
old label count: korean      799
indian      598
chinese     442
japanese    320
thai        289
Name: cuisine, dtype: int64
```

```python
# 레이블 및 기능을 포함한 균형 잡힌 데이터를 파일로 내보낼 수 있는 새 데이터 프레임에 저장
transformed_df = pd.concat([transformed_label_df,transformed_feature_df],axis=1, join='outer')
# 데이터를 한 번 더 살펴보기
transformed_df.head()
```

|  |	cuisine | almond | angelica | anise | anise_seed | apple | apple_brandy | apricot	| armagnac | artemisia | ... | whiskey | white_bread | white_wine | whole_grain_wheat_flour | wine | wood | yam | yeast |	yogurt |	 zucchini |
| --- | ---------- | ------- | ------ | -------- | ----- | ---------- | ----- | ------------ | ------- | -------- | --- | ------- | ----------- | ---------- | ----------------------- | ---- | ---- | --- | ----- | ------ | -------- |
|0|	indian	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|1|	indian	|1	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|2|	indian	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|3|	indian	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|4|	indian	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	1|	0|
|...|	...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...	|...|	...|	...|	...|
|3990|	thai	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|3991|	thai	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|3992|	thai	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|3993|	thai	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|
|3994|	thai	|0	|0	|0	|0	|0	|0	|0	|0	|0	|...	|0	|0	|0	|0	|0	|0	|0|	0|	0|	0|

3995 rows × 381 columns

```python
# 데이터 정보 확인
transformed_df.info()
# 다음 교육에서 사용할 수 있도록 데이터 복사본을 저장
transformed_df.to_csv("../data/cleaned_cuisines.csv")
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3995 entries, 0 to 3994
Columns: 381 entries, cuisine to zucchini
dtypes: int64(380), object(1)
memory usage: 11.6+ MB
```

🎈 **데이터 폴더를 살펴보고 이진 또는 다중 클래스 분류에 적합한 데이터 셋이 있는지 확인하고 해당 데이터 세트에 대해 어떤 질문을 할지 생각해보기**

## Classifiers 1

### 요리 분류기
- "Introduction"에서 저장한 모든 음식에 대한 **균형 잡힌 깨끗한 데이터로 가득 찬 데이터 세트**를 사용할 것이다.
- 이 데이터 세트를 **다양한 분류기**와 함께 사용하여 *재료 그룹을 기반*으로 **특정 국가 음식을 예측**할 수 있다.
- 이렇게 하는 동안, **분류 작업**에 알고리즘을 활용할 수 있는 몇 가지 방법에 대해 자세히 알아볼 수 있다.

### 연습
```python
# 파일 불러오기 
import pandas as pd
cuisines_df = pd.read_csv("cleaned_cuisines.csv")
cuisines_df.head()
```


|     | Unnamed: 0| cuisine | almond | angelica | anise | anise_seed | apple | apple_brandy | apricot | armagnac | ... | whiskey | white_bread | white_wine | whole_grain_wheat_flour | wine | wood | yam | yeast | yogurt | zucchini |
| --- | ---------- | ------- | ------ | -------- | ----- | ---------- | ----- | ------------ | ------- | -------- | --- | ------- | ----------- | ---------- | ----------------------- | ---- | ---- | --- | ----- | ------ | -------- |
| 0   | 0      | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 1   | 1        | indian  | 1      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 2   | 2        | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 3   | 3        | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 4   | 4       | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 1      | 0        |


```python
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.metrics import accuracy_score,precision_score,confusion_matrix,classification_report, precision_recall_curve
from sklearn.svm import SVC
import numpy as np

# 훈련을 위해 두개의 데이터프레임으로 X와 Y좌표를 나눈다.
# 요리는 라벨 데이터프레임이 될 수 있다.
cuisines_label_df = cuisines_df['cuisine']
cuisines_label_df.head()
```
```
0    indian
1    indian
2    indian
3    indian
4    indian
Name: cuisine, dtype: object
```


```python
# 'unnamed: 0'의 열과 요리 열을 'drop()'을 호출하여 삭제 -> 나머지 데이터를 교육 가능한 기능으로 저장
cuisines_feature_df = cuisines_df.drop(['Unnamed: 0', 'cuisine'], axis=1)
cuisines_feature_df.head()
```

|     | almond | angelica | anise | anise_seed | apple | apple_brandy | apricot | armagnac | ... | whiskey | white_bread | white_wine | whole_grain_wheat_flour | wine | wood | yam | yeast | yogurt | zucchini |
|------- | ------ | -------- | ----- | ---------- | ----- | ------------ | ------- | -------- | --- | ------- | ----------- | ---------- | ----------------------- | ---- | ---- | --- | ----- | ------ | -------- |
| 0   | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 1   | 1      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 2    | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 3    | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
| 4  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 1      | 0        |

모델을 훈련할 준비가 됐다 !

### 분류기 선택









## Classifiers 2 

## Applied


___

## 참고 : 
https://github.com/codingalzi/ML-For-Beginners/tree/main/4-Classification

