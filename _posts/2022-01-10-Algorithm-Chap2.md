---
layout: post
title: Algorithm - Chap 2
subtitle: Chap2
categories: Classic_Computer_Science
tags: Algorithm
---

## Chap2 - 검색 문제


### 2.1 DNA 검색
유전자는 일반적으로 프로그램에서 문자 A, C, G, T의 시퀀스로 표현하며, 각 문자는 **뉴클레오타이드**를 나타내고, 세 개의 뉴클레오타이드 조합을 **코돈**이라고 한다.
특정 아미노산에 대한 코돈 코드는 다른 아미노산과 함께 단백질을 형성할 수 있다.

즉, 문자(A, C, T, G) 중 하나로 표현하는 뉴클레오타이드가 세 개가 모이면 코돈이 구성되고, 다수의 코돈으로는 유전자를 구성한다.

#### (1) DNA 정렬
```python
from enum import IntEnum
from typing import Tuple, List

Nucleotide: IntEnum = IntEnum('Nucleotide', ('A', 'C', 'G', 'T'))
``` 
뉴클레오타이드는 Enum 타입 대신 비교연산자를 사용할 수 있는 IntEnum 타입을 사용한다. 

<p> </p>

**이러한 데이터 타입은 구현하려는 검색 알고리즘에서 작동할 수 있어야 한다.**


```python
# 코돈은 뉴클레오타이드 세 개의 튜플로 정의, 유전자는 코돈의 리스트로 정의
Codon = Tuple[Nucleotide, Nucleotide, Nucleotide]
Gene = List[Codon]
```

<p> </p>


```python
gene_str: str = "ACGTGGCTCTCTAACGTACGTACGTACGGGGTTTATATATACCCTAGGACTCCCTTT" # 유전자 문자열 정의

# 문자열 타입을 Gene 타입 앨리어스로 변환
def string_to_gene(s: str) -> Gene:
    gene: Gene = []
    for i in range(0, len(s), 3):
        if (i + 2) >= len(s):  # don't run off end!
            return gene
        #  initialize codon out of three nucleotides
        codon: Codon = (Nucleotide[s[i]], Nucleotide[s[i + 1]], Nucleotide[s[i + 2]])
        gene.append(codon)  # add codon to gene
    return gene
```

`string_to_gene()`함수는 문자열인자를 취하고, 반복문에서 이를 처리한다.

___
## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
