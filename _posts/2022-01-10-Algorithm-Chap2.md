---
layout: post
title: Algorithm - Chap 2
subtitle: Chap2
categories: Classic_Computer_Science
tags: Algorithm
---

## Chap2 - 검색 문제


### 2.1 DNA 검색
유전자는 일반적으로 프로그램에서 문자 A, C, G, T의 시퀀스로 표현한다.
각 문자는 **뉴클레오타이드**를 나타내고, 세 개의 뉴클레오타이드 조합을 **코돈**이라고 한다.
특정 아미노산에 대한 코돈 코드는 다른 아미노산과 함께 단백질을 형성할 수 있다.

#### (1) DNA 정렬
```python
from enum import IntEnum
from typing import Tuple, List

Nucleotide: IntEnum = IntEnum('Nucleotid', ('A', 'C', 'G', 'T'))
``` 
뉴클레오타이드는 Enum 타입 대신 IntEnum 타입을 사용한다.




___
## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
