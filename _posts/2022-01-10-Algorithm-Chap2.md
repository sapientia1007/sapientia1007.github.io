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
        if (i + 2) >= len(s):  # 현재 위치 다음에 2개의 문자가 없으면 실행하지 않는다.
            return gene
        #  3개의 늌틀레오타이드에서 코돈을 초기화
        codon: Codon = (Nucleotide[s[i]], Nucleotide[s[i + 1]], Nucleotide[s[i + 2]])
        gene.append(codon)  # 코돈을 유전자에 
    return gene
    
my_gene: Gene = string_to_gene(gene_str)
```

- `string_to_gene()`함수는 문자열 인자를 취하고, 반복문에서 이를 처리하는 것으로, 문자열 변수를 Gene 타입 앨리어스로 변환하는데 사용하낟.
- 세 개의 문자를 코돈으로 변환하여 새 리스트 변수 Gene 끝에 추가한다.
- 문자열 현재 위치에서 다음 두 개의 뉴클레오타이드 문자가 없다면, 불완전한 유전자의 끝에 도달했다는 것으로 알고, 마지막 하나 또는 두 개의 뉴클레오타이드를 건너뛴다.


#### (2) 선형 검색

**선형 검색(Linear Search)** 은 **유전자에서 특정 코돈이 존재하는지 여부를 확인**하는 것이다. 
> 선형 검색은 찾고자 하는 요소가 발견되거나 자료구조의 끝에 도달할 때까지 순서대로 모든 요소를 훑어본다.
> 선형 검색은 가장 간단하고, 자연스럽고, 명백한 방법으로 검색한다.
> **최악**의 경우 선형 검색은 *자료구조의 모든 요소를 거쳐야* 하므로 **최악의 시간복잡도는 `O(n)`** 이다. (n : 자료구조의 요소 수)
> 단순히 자료구조의 모든 요소를 탐색하면서 탐색할 항목과 동등한지 확인한다.

```python
# Gene 타입 앨리어스와 Codon 타입 앨리어스를 인자로 취하는 선형 검색 함수를 정의
def linear_contains(gene: Gene, key_codon: Codon) -> bool:
    for codon in gene:
        if codon == key_codon:
            return True
    return False


acg: Codon = (Nucleotide.A, Nucleotide.C, Nucleotide.G)
gat: Codon = (Nucleotide.G, Nucleotide.A, Nucleotide.T)
print(linear_contains(my_gene, acg))  # 참
print(linear_contains(my_gene, gat))  # 

# 앞에서 정의한 my_gene 변수와 각각의 acg, gat 변수를 함수의 인자로 전달하여 선형 검색
```




#### (3) 이진 검색

**이진 검색(Binary Search)** 은 모든 요소를 살펴보는 선형 검색보다 빠른 검색 방법이지만, **해당 자료구조의 저장 순서를 미리 알고 있어야** 한다.
- 자료구조가 정렬되어 있고, 그 인덱스로 항목에 즉시 접근할 수 있는 경우 이진 검색을 할 수 있다.
- 파이썬에서는 리스트를 사용하여 이진 검색을 수행하는 방식으로 작동한다.
- 이진 검색은 정렬된 요소들의 범위에서 중간 요소를 검색하여 찾고자 하는 요소와 비교한다.
- 해당 비교를 기준으로 범위를 바으로 줄이고 다시 이진 검색을 수행하는 방식으로 작동한다.
- 이진 검색은 *검색 공간을 계속해서 절반으로 줄이므로* **최악의 경우 시간복잡도는 `O(lg n)`** 이다.
- 이진 검색은 선형 검색과달리 정렬된 자료구조가 필요하며, 정렬에는 시간이 소요된다. (최적의 정렬 알고리즘의 시간복잡도는 `O(n lg n)`)

<p> </p>
**검색을 딱 한번 수행**하려 하고 *원본 자료구조가 정렬되어 있지 않다*면, **선형 검색**이 더 효율적이다.

**검색이 여러 번 수행**된다면, *개별 검색의 시간 비용을 절약*하는 점에서 **이진 검색**이 더 효율적이다.

**유전자 코돈에 대한 이진 검색 함수는 다른 타입의 데이터에 대한 이진 검색 함수를 작성하는 것과 크게 다르지 않다.**
왜냐하면 Codon 타입은 다른 타입과 비교할 수 있고, Gene 타입은 리스트이기 때문이다.

``` python
def binary_contains(gene: Gene, key_codon: Codon) -> bool:
    # 전체 유전자 리스트 범위에서 찾기 시작
    low: int = 0
    high: int = len(gene) - 1
    
    # 검색 공간이 있을 때까지 계속 검색을 수행, low가 high보다 크면 리스트에서 해당 검색할 범위가 더 이상 없다는 것을 의미
    while low <= high:  
        mid: int = (low + high) // 2 # 검색 범위를 반으로 나누기
        if gene[mid] < key_codon:
            low = mid + 1 # 검색할 요소가 범위의 중간 요소 뒤에 있는 경우 mid 변수에 1을 더하여 중간 요소 다음 위치로 low 변수 수정
        elif gene[mid] > key_codon:
            high = mid - 1 # 검색할 요소가 범위의 중간 요소 앞에 있는 경우 mid 변수에 1을 빼서 중간 요소 이전 위치로 high 변수 수정
        else:
            return True
    return False # 실행되지 않고 반복문이 끝나면, 검색할 요소가 발견되지 않았음을 나타낸다.


my_sorted_gene: Gene = sorted(my_gene) # 정렬 
print(binary_contains(my_sorted_gene, acg))  # 참
print(binary_contains(my_sorted_gene, gat))  # 거짓

```

#### (4) 제네릭 검색 예제

`linear_contains()`와 `binary_contains()` 함수는 파이썬의 거의 모든 시퀀스에서 동작하도록 일반화할 수 있다.

아래 일반화된 코드는 이전 코드와 거의 동일하며, 일부 이름과 타입 힌트만 바뀌었다.
```python
from __future__ import annotations
from typing import TypeVar, Iterable, Sequence, Generic, List, Callable, Set, Deque, Dict, Any, Optional
from typing_extensions import Protocol
from heapq import heappush, heappop

T = TypeVar('T')


def linear_contains(iterable: Iterable[T], key: T) -> bool:
    for item in iterable:
        if item == key:
            return True
    return False


C = TypeVar("C", bound="Comparable")


class Comparable(Protocol):
    def __eq__(self, other: Any) -> bool:
        ...

    def __lt__(self: C, other: C) -> bool:
        ...

    def __gt__(self: C, other: C) -> bool:
        return (not self < other) and self != other

    def __le__(self: C, other: C) -> bool:
        return self < other or self == other

    def __ge__(self: C, other: C) -> bool:
        return not self < other


def binary_contains(sequence: Sequence[C], key: C) -> bool:
    low: int = 0
    high: int = len(sequence) - 1
    while low <= high:  # 검색 공간이 있을 때까지 
        mid: int = (low + high) // 2
        if sequence[mid] < key:
            low = mid + 1
        elif sequence[mid] > key:
            high = mid - 1
        else:
            return True
    return False

if __name__ == "__main__":
    print(linear_contains([1, 5, 15, 15, 15, 15, 20], 5))  # True
    print(binary_contains(["a", "d", "e", "f", "z"], "f"))  # True
    print(binary_contains(["john", "mark", "ronald", "sarah"], "sheila"))  # False
```
이제 다른 데이터 타입의 선형 검색과 이진 검색을 수행할 수 있다. 이 함수는 거의 모든 파이썬 컬렉션에서 재사용할 수 있다.

위 예제에서 **타입 힌트**를 위해 `Comparable 클래스`를 구현해야 하는데, `Comparable 타입`은 비교 연산자를 구현하는 타입이다.  


***

### 2.2 미로 
**미로**의 경로를 문자를 이용해서 찾고, **너비 우선 탐색, 깊이 우선 탐색, A* 알고리즘**을 구현할 것이다.


우리 미로는 셀(Cell)의 2차원 격자로, Cell 클래스는 문자열 열거형(enum)이고, `" "`는 미로의 빈 공간, `"X"`는 막힌 공간을 나타낸다.

```python
from enum import Enum
from typing import List, NamedTuple, Callable, Optional
import random
from math import sqrt
from generic_search import dfs, bfs, node_to_path, astar, Node


class Cell(str, Enum):
    EMPTY = " "
    BLOCKED = "X"
    START = "S"
    GOAL = "G"
    PATH = "*"


class MazeLocation(NamedTuple):
    row: int
    column: int

```

#### (1) 미로 무작위로 생성

#### (2) 기타 미로 세부사항

#### (3) 깊이 우선 탐색

#### (4) 너비 우선 탐색

#### (5) A* 알고리즘

***

### 2.3 선교사와 식인종 문제


#### (1) 문제 나타내기

#### (2) 문제 풀이

***

### 2.4 적용 사례


***

### 2.5 연습문제

#### (1)


#### (2)

#### (3)

___
## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
