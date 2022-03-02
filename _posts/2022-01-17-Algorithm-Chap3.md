---
layout: post
title: Algorithm - Chap 3
subtitle: Chap3
categories: Classic_Computer_Science
tags: Algorithm
use_math: true
---

## Chap3 - 제약 충족 문제
컴퓨터를 사용하여 해결할 수 있는 많은 문제는 **제약 충족 문제(Constraint-Satisfaction Problem = CSP)** 로 크게 분류할 수 있다.

- **제약 충족 문제**는 **도메인**이라는 범위에 속하는 값을 갖는 **변수**로 구성된다.
- *제약 충족 문제*가 해결되려면, 변수 사이의 **제약 조건**이 충족되어야 한다.
 
   → 제약 충족 문제의 세 가지 핵심 개념 : 변수 / 도메인 / 제약 조건 
   

프롤로그(Prolog)나 피캣(Picat)과 같은 프로그래밍 언어는 제약 충족 문제를 해결할 수 있는 함수를 제공한다.

다른 언어의 일반적인 기술은 **백트래킹 검색**과 이를 향상시키는 **몇 가지 휴리스틱**을 통합하여 `프레임워크`를 구축한다.


### 3.1 제약 충족 문제 프레임워크 구현하기

- 제약 조건은 `Constraint 클래스`로 정의한다.

  → 이 클래스는 `제약 조건 변수(variables)`와 이를 충족하는지 검사하는 `메서드(satisfied())`로 구성된다.
  
  → 제약 조건이 충족되었는지 판단하는 작업은 *제약 충족 문제의 핵심 로직*이다.
- `Constraint 클래스`를 **추상 클래스** 정의하여 기본 구현을 **오버라이드**한다.
- **추상 클래스는** *메서드*에 `@abstractmethod 데커레이터`를 사용하여 **인스턴스화되지 않는다.**

  → 이 메서드는 실제 구현하는 서브클래스의 메서드에 의해 오버라이드된다.
  
```python
from typing import Generic, TypeVar, Dict, List, Optional
from abc import ABC, abstractmethod

V = TypeVar('V') # 변수(Variable) 타입
D = TypeVar('D') # 도메인(Domain) 타입

# 모든 제약 조건에 대한 베이스 클래스
class Constraint(Generic[V, D], ABC) :
    # 제약 조건 변수
    def __init__(self, variables : List[V]) -> None :
      self.variables = variables
  
    # 서브클래스 메서드에 의해 오버라이드된다.
    @abstractmethod
    def satisfied(self, assignment : Dict[V, D]) -> bool :
      ...
```

제약 충족 문제 프레임워크의 핵심은 **CSP 클래스**다.

  → 이 클래스는 변수, 도메인, 제약 조건을 저장한다.
  → 타입 힌트에서 제네릭을 사용하여 모든 종류의 변수(V) 및 도메인(D) 값을 유연하게 처리한다.
```python
# 제약 충족 문제는 타입 V의 '변수'와 범위를 나타내는 타입 D의 '도메인',
# 특정 변수의 도메인이 유효한지 확인하는 '제약 조건'으로 구성된다.

# 변수 컬렉션은 변수의 리스트
# 도메인 컬렉션은 변수에 가능한 값 리스트를 매핑하는 딕셔너리(각 변수의 도메인)
# 제약 조건 컬렉션은 각 변수에 제약 조건(Constraint 클래스) 리스트로 매핑된 딕셔너리
class CSP(Generic[V, D]):
    # __init__() 특수 메서드에서 딕셔너리 타입의 제약 조건을 생성
    def __init__(self, variables: List[V], domains: Dict[V, List[D]]) -> None:
        self.variables: List[V] = variables # 제약 조건을 확인할 변수
        self.domains: Dict[V, List[D]] = domains # 각 변수의 도메인
        self.constraints: Dict[V, List[Constraint[V, D]]] = {}
        for variable in self.variables:
            self.constraints[variable] = []
            if variable not in self.domains:
                raise LookupError("모든 변수에 도메인이 할당되어야 합니다.")

    # add_constraint() 메서드는 모든 변수에 대해 제약 조건을 확인하고, 각 제약 조건 매핑에 자신을 추가
    def add_constraint(self, constraint: Constraint[V, D]) -> None:
        for variable in constraint.variables:
            if variable not in self.variables:
                raise LookupError("Variable in constraint not in CSP")
            else:
                self.constraints[variable].append(constraint)
```

주어진 변수 구성과 선택된 도메인값이 제약 조건을 충족시키는지 알 수 있기 위해 **주어진 변수에 대한 모든 제약 조건을 할당하고 비교하여 할당 변숫값이 제약 조건을 만족하는지 확인하는 `consistent() 메서드`** 를 사용한다. 
```python
# 주어진 변수의 모든 제약 조건을 검사하여 assignment 값이 일관적인지 확인
def consistent(self, variable: V, assignment: Dict[V, D]) -> bool :
    for constraint in self.constraints[variable] :
        if not constraint.satisfied(assignment) :
            return False # 변수에 할당된 제약 조건이 충족되지 않음
     return True # 할당이 모든 제약조건을 충족
```

이러한 제약 충족 프레임워크는 간단한 **백트레킹(backtracking)** 에 사용된다.
  → 백트래킹은 검색에서 벽에 부딪혔을 때 이 지점에 대한 결정을 내린 마지막 지점으로 돌아가 다른 경로를 선택하는 방안 ( 깊이 우선 탐색과 유사 ) 
  
```python
# CSP 클래스에 추가

def backtracking_search(self, assignment: Dict[V, D] = {}) -> Optional[Dict[V,D]] :
    # 재귀적 검색에 대한 기저 조건은 모든 변수에 대한 유효 할당을 찾는것, 유효 할당을 찾았다면 솔루션의 첫 번째 인스턴스를 반환
    if len(assignment) == len(self.variables) :
        return assignment
    
    # 할당되지 않은 모든 변수를 가져온다.
    unassigned : List[V] = [v for v in self.variables if v not in assignment]
    
    # 할당되지 않은 첫 번째 변수의 가능한 모든 도메인 값을 가져온다.
    first : V = unassigned[0]
    for value in self.domains[first] : 
        local_assignment = assignment.copy()
        local_assignment[first] = value
        # local_assignment 값이 일관적이면 재귀 호출
        if self.consistent(first, local_assignment) :
            result : Optional[Dict[V, D]] = self.backtracking_search(local_assignment)
            # 결과를 찾지 못하면 백트래킹을 종료
            if result is not None : 
                return result
    return None # 솔루션 없음
```

### 3.2 호주 지도 색칠 문제
![aus_map](http://jjhcom.github.io/assets/images/banners/aus_map_00.jpg) : <https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=thatsnothing&logNo=220362024301>

호주 지도에서 인접한 두 지역은 같은 색을 사용할 수 없다는 조건으로 분할된 지역을 3가지 색을 이용하여 칠한다고 가정한다.

호주 지도 색칠하기는 사소하면서 쉽게 접근할 수 있기 때문에 **백트래킹**과 **제약 충족 문제**를 위한 첫 번째 문제로 적당하다.

**호주 지도 색칠 문제를 `제약 충족 문제`로 모델링**하기 위해서는 *변수, 도메인, 제약 조건*을 정의해야 한다.
- 변수 : 호주의 7개 지역(뉴사우스웨일스, 빅토리아, 퀸즐랜드, 사우스 오스트레일리아, 웨스턴 오스트레일리아, 태즈메이니아, 노던 준주) → 변수는 문자열로 모델링
- 각 변수의 도메인에 할당할 수 있는 색상은 세가지(빨강, 파랑, 노랑)
- 제약 조건은 인접한 지역은 같은 색을 칠할 수 없으므로 인접한 지역에 따라 달라진다. → 이진 제약 조건(두 변수 사이의 제약 조건)을 사용할 수 있다.


- 이러한 **이진 제약 조건**을 구현하기 위해서 `Constraint 베이스 클래스`를 이용한다.
- `MapColoringConstraint 서브클래스` 생성자에는 경계를 공유하는 두 지역에 대한 변수가 있다.
- *오버라이드*된 `satisfied() 메서드`는 먼저 두 지역에 할당된 도메인 값(색상)이 있는지 확인한다.
- 두 지역의 색상이 같으면 제약 조건이 충족되지 않는다는 것을 의미한다.

```python
# MapColoringConstraint 클래스는 타입 힌트에서 제네릭하지 않다. 문자열 타입의 변수와 도메인이 매개변수화된 Constraint 클래스를 상속받는다.
# 지역 간 제약 조건을 확인하는 방법 구현

from csp, import Constraint, CSP
from typing import Dict, List, Optional

class MapColoringConstraint(Constraint[str, str]) :
    def __init__(self, place : str1, place2 : str) -> None :
        super().__init__([place1, place2])
        self.place1 : str = place1
        self.place2 : str = place2
        
    def saatisfied(self, assignment : Dict[str, str]) -> bool : 
        if self.place1 not in assignment or self.place2 not in assignment  :
            return True
        return assignment[self.place1] != assignment[self.place2]
```

``` python
# 호주 지도 색칠 문제는 CSP 클래스를 사용하여 단순히 변수와 도메인, 제약 조건을 추가

if __name__ == "__main__":
    variables: List[str] = ["웨스턴 오스트레일리아", "노던 준주", "사우스 오스트레일리아", 
                           "퀸즐랜드", "뉴사우스웨일스", "빅토리아", "태즈메이니아"]
    domains: Dict[str, List[str]] = {}
    for variable in variables:
        domains[variable] = ["빨강", "초록", "파랑"]
    csp: CSP[str, str] = CSP(variables, domains)
    csp.add_constraint(MapColoringConstraint("웨스턴 오스트레일리아", "노던 준주"))
    csp.add_constraint(MapColoringConstraint("웨스턴 오스트레일리아", "사우스 오스트레일리아"))
    csp.add_constraint(MapColoringConstraint("사우스 오스트레일리아", "노던 준주"))
    csp.add_constraint(MapColoringConstraint("퀸즐랜드", "노던 준주"))
    csp.add_constraint(MapColoringConstraint("퀸즐랜드", "사우스 오스트레일리아"))
    csp.add_constraint(MapColoringConstraint("퀸즐랜드", "뉴사우스웨일스"))
    csp.add_constraint(MapColoringConstraint("뉴사우스웨일스", "사우스 오스트레일리아"))
    csp.add_constraint(MapColoringConstraint("빅토리아", "사우스 오스트레일리아"))
    csp.add_constraint(MapColoringConstraint("빅토리아", "뉴사우스웨일스"))
    csp.add_constraint(MapColoringConstraint("빅토리아", "태즈메이니아"))
    
    # 호주 지도를 색칠
    solution: Optional[Dict[str, str]] = csp.backtracking_search()
    if solution is None:
        print("답이 없습니다!")
    else:
        print(solution)
```

### 3.3 여덟 퀸 문제
![queens](http://jjhcom.github.io/assets/images/banners/queen.png) : <https://terms.naver.com/entry.naver?docId=5667830&cid=60205&categoryId=60205>
- 체스보드는 8x8 격자로 데어 있고, 퀸은 체스보드의 모든 행과 열, 대각선으로 이동할 수 있다. 
- 퀸의 이동 경로에 적군 말이 있다면, 그 말의 위치로 이동하여 획득할 수 있다.
- 여덟 퀸 문제는 한 퀸이 다른 퀸을 공격하지 않도록 여덟 개의 퀸을 체스보드에 배치하는 것이다.
- 여덟 퀸은 서로 공격할 수 없다.


**체스보드**를 나타내기 위해 *정수의 행과 열*을 할당하고, 각 **여덟 개의 퀸**이 **같은 열애 배치되지 않도록** 순차적으로 1열에서 8열까지 배치한다.
- 제약 충족 문제의 변수 : 퀸의 열
- 도메인 : 1에서 8 행
- 제약 조건 : 두 퀸이 같은 줄에 있거나 대각선에 있는지 확인

```python
# 제약 조건
from csp import Constraint, CSP
from typing import Dict, List, Optional


class QueensConstraint(Constraint[int, int]):
    def __init__(self, columns: List[int]) -> None:
        super().__init__(columns)
        self.columns: List[int] = columns

    def satisfied(self, assignment: Dict[int, int]) -> bool:
        # q1c = 퀸 1 열, q1r = 퀸 1 행
        for q1c, q1r in assignment.items():
            # q2c = 퀸 2 c열
            for q2c in range(q1c + 1, len(self.columns) + 1):
                if q2c in assignment:
                    q2r: int = assignment[q2c] # q2r = 퀸 2 행
                    if q1r == q2r: # 같은 행?
                        return False
                    if abs(q1r - q2r) == abs(q1c - q2c): # 같은 대각선?
                        return False
        return True # 충돌 없음
        
        
if __name__ == "__main__":
    columns: List[int] = [1, 2, 3, 4, 5, 6, 7, 8]
    rows: Dict[int, List[int]] = {}
    for column in columns:
        rows[column] = [1, 2, 3, 4, 5, 6, 7, 8]
    csp: CSP[int, int] = CSP(columns, rows)
    csp.add_constraint(QueensConstraint(columns))
    solution: Optional[Dict[int, int]] = csp.backtracking_search()
    if solution is None:
        print("답을 찾을 수 없습니다!")
    else:
        print(solution)
```
**호주 지도 색칠 문제**를 해결한 *제약 충족 문제 해결 프레임워크*를 재사용하여 다른 유형의 문제인 **여덟 퀸 문제**를 해결했다.

특정 애플리케이션에서 필요로 하는 성능 최적화 작업을 제외하고, 알고리즘은 가능한 한 광범위하게 적용될 수 있어야 한다 = 제네릭의 힘




### 3.4 단어 검색
**단어 검색**은 격자에서 *행과 열, 대각선을 따라 배치된 특정 단어*를 찾는 문제이다.
- 제약 충족 문제 : 찾으려는 단어를 격자에 배치하는 것
- 변수 : 단어
- 도메인 : 단어의 위치

*문제를 간단하게 하기 위해 격자에 중복 단어는 포함시키지 않는다.*

*단어 검색 문제의 데이터 타입은 미로 찾기 문제와 조금 비슷하다*
```python
from typing import NamedTuple, List, Dict, Optional
from random import choice
from string import ascii_uppercase
from csp import CSP, Constraint

Grid = List[List[str]]  # 격자를 위한 타입 앨리어스


class GridLocation(NamedTuple):
    row: int
    column: int


def generate_grid(rows: int, columns: int) -> Grid:
    # 임의 문자로 격자를 초기화
    return [[choice(ascii_uppercase) for c in range(columns)] for r in range(rows)]


def display_grid(grid: Grid) -> None:
    for row in grid:
        print("".join(row))
```
특정 단어가 격자에 들어갈 수 있는 위치를 파악하기 위해 *도메인을 생성*한다.

**단어의 도메인**은 모든 문자의 가능한 위치 리스트의 리스트(`List(List[GridLocation])`)이다.

**단어**는 경계 내에 있는 행, 열 또는 대각선 안에 있다 → 단어는 각자의 경계 밖을 벗어날 수 없다.

```python
# 단어의 잠재적 위치 범위에서 리스트 컴프리헨션은 클래스 생성자를 이용하여 범위를 GirdLocation 리스트로 변환
def generate_domain(word: str, grid: Grid) -> List[List[GridLocation]]:
    domain: List[List[GridLocation]] = []
    height: int = len(grid)
    width: int = len(grid[0])
    length: int = len(word)
    for row in range(height):
        for col in range(width):
            columns: range = range(col, col + length)
            rows: range = range(row, row + length)
            if col + length <= width:
                # 왼쪽 -> 오른쪽
                domain.append([GridLocation(row, c) for c in columns])
                # 대각선 오른쪽 아래로
                if row + length <= height:
                    domain.append([GridLocation(r, col + (r - row)) for r in rows])
            if row + length <= height:
                # 위 -> 아래
                domain.append([GridLocation(r, col) for r in rows])
                # 대각선 외쪽 아래로
                if col - length >= 0:
                    domain.append([GridLocation(r, col - (r - row)) for r in rows])
    return domain
    
# 단어의 위치 범위가 맞는지 확인하기 위해 단어 검색 제약 조건을 구현
class WordSearchConstraint(Constraint[str, List[GridLocation]]):
    def __init__(self, words: List[str]) -> None:
        super().__init__(words)
        self.words: List[str] = words

    def satisfied(self, assignment: Dict[str, List[GridLocation]]) -> bool:
        # 중복된 격자 위치가 있다면 그 위치는 겹치는 부분 -> 셋을 사용
        all_locations = [locs for values in assignment.values() for locs in values]
        return len(set(all_locations)) == len(all_locations)


if __name__ == "__main__":
    grid: Grid = generate_grid(9, 9)
    words: List[str] = ["MATTHEW", "JOE", "MARY", "SARAH", "SALLY"]
    locations: Dict[str, List[List[GridLocation]]] = {}
    for word in words:
        locations[word] = generate_domain(word, grid)
    csp: CSP[str, List[GridLocation]] = CSP(words, locations)
    csp.add_constraint(WordSearchConstraint(words))
    solution: Optional[Dict[str, List[GridLocation]]] = csp.backtracking_search()
    if solution is None:
        print("답을 찾을 수 없습니다!")
    else:
        for word, grid_locations in solution.items():
            # 50% 확률로 grid_locations 반전(reverse)한다.
            if choice([True, False]):
                grid_locations.reverse()
            for index, letter in enumerate(word):
                (row, col) = (grid_locations[index].row, grid_locations[index].column)
                grid[row][col] = letter
        display_grid(grid)
```

### 3.5 SEND + MORE = MONEY
**SEND + MORE = MONEY**는 *복면산 퍼즐*이다.

- 문자로 표현된 수식에서 각 문자가 나타내는 숫자를 알아내는 문자이다.
- 각 문자는 한 자리 숫자(0~9)를 나타낸다.
- 서로 다른 문자는 같은 숫자를 나타낼 수 없다.
- 문자 반복은 숫자 반복을 의미한다.

```python
from csp import Constraint, CSP
from typing import Dict, List, Optional


class SendMoreMoneyConstraint(Constraint[str, int]):
    def __init__(self, letters: List[str]) -> None:
        super().__init__(letters)
        self.letters: List[str] = letters

    def satisfied(self, assignment: Dict[str, int]) -> bool:
        if len(set(assignment.values())) < len(assignment):
        # 중복된 값이 있다면 답이 아니므로 False 반환
            return False

        # 모든 변수에 숫자를 할당해서, 계산이 맞는지 확인
        if len(assignment) == len(self.letters):
            s: int = assignment["S"]
            e: int = assignment["E"]
            n: int = assignment["N"]
            d: int = assignment["D"]
            m: int = assignment["M"]
            o: int = assignment["O"]
            r: int = assignment["R"]
            y: int = assignment["Y"]
            send: int = s * 1000 + e * 100 + n * 10 + d
            more: int = m * 1000 + o * 100 + r * 10 + e
            money: int = m * 10000 + o * 1000 + n * 100 + e * 10 + y
            return send + more == money # 수식에 지정된 할당이 올바른지 확인
        return True # 충돌 없음


if __name__ == "__main__":
    letters: List[str] = ["S", "E", "N", "D", "M", "O", "R", "Y"]
    possible_digits: Dict[str, List[int]] = {}
    for letter in letters:
        possible_digits[letter] = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    # M에 0을 할당하지 않기 위해 취한 조치로, 1을 미리 할당
    possible_digits["M"] = [1]  # 답은 0으로 시작하지 않는다.
    csp: CSP[str, int] = CSP(letters, possible_digits)
    csp.add_constraint(SendMoreMoneyConstraint(letters))
    solution: Optional[Dict[str, int]] = csp.backtracking_search()
    if solution is None:
        print("답을 찾을 수 없습니다!")
    else:
        print(solution)
```


### 3.6 회로판 레이아웃
**회로판에 여러 다양한 사각형 모양의 칩을 장착**하는 제약 충족 문제이다.
- 회로판 레이아웃 문제는 단어 검색 문제와 비슷하다.
- 단어 검색 문제가 1 x N 사각형의 단어를 사용한다면, 회로판 레이아웃에는 M x N 사각형의 칩을 사용한다.
- 단어 검색 문제와 마찬가지로 사각형은 서로 겹칠 수 없다.
- 사각형이 대각선으로 배치될 수 없기 때문에 단어 검색보다 더 간단하다.
- 회로판 칩의 직사각형의 너비가 다양하다.

### 3.7 적용사례
**제약 조건 문제**는 일반적으로 스케줄링에 사용된다.*(제약 충족 문제는 모션 플래닝(motion planning)에도 사용된다)*

이 장에서는 단순한 `백트래킹`, `깊이 우선 탐색`, `문제 해결 프레임워크`를 구축했다.

**검색**을 효율적으로 만드는 직관인 **휴리스틱(`A * 알고리즘`)** 을 사용하면 *제약 충족 문제를 크게 향상시킬* 수 있다.

`백트래킹`보다 더 새로운 기술인 `제약식 확산법(constraint propagation)`은 애플리케이션을 위한 효율적인 방안이다.


### 3.8 연습 문제

#### (1)
**WordSearchConstraint 클래스를 수정하여 중복 단어를 허용하는 단어 검색을 구현하라.**

#### (2)
**아직 작성하지 않았다면, 3.6절에서 제시했던 회로판 레이아웃 문제를 해결하는 코드를 작성하라.**

```python
from typing import NamedTuple, List, Dict, Optional, Tuple
from csp import Constraint, CSP

Grid = List[List[str]]

class GridLocation(NamedTuple):
    row:int
    column:int

def generate_grid(rows:int, columns:int) -> Grid:
    return [["-" for c in range(columns)] for r in range(rows)]

def display_grid(grid:Grid) -> None:
    for row in grid:
        print(" ".join(row))

def generate_domain(layout:tuple, grid:Grid) -> List[List[GridLocation]]:
    domain:List[List[GridLocation]] = []
    height:int = len(grid)
    width:int = len(grid[0])
    layout_width:int = layout[1]
    layout_height:int = layout[2]
    for row in range(height):
        for col in range(width):
            columns:range = range(col, col + layout_width)
            rows:range = range(row, row + layout_height)
            if (col + layout_width <= width) and (row + layout_height <= height):
                domain.append([GridLocation(r, c) for c in columns for r in rows])
    return domain


class LayoutConstraint(Constraint[tuple, List[GridLocation]]):
    def __init__(self, layouts:List[Tuple[str, int, int]]) -> None:
        super().__init__(layouts)
        self.layouts:List[Tuple[str, int, int]] = layouts

    def satisfied(self, assignment:Dict[tuple, List[GridLocation]]) -> bool:
        all_locations = [locs for values in assignment.values() for locs in values]
        return len(set(all_locations)) == len(all_locations)


if __name__ == "__main__":
    grid:Grid = generate_grid(9, 9)
    layouts:List[Tuple[str, int, int]] = [("1", 2, 6), ("2", 4, 4), ("3", 1, 5), ("4", 3, 3), ("5", 2, 4)]
    locations:Dict[str, List[List[GridLocation]]] = {}
    for layout in layouts:
        locations[layout] = generate_domain(layout, grid)

    csp:CSP[str, List[GridLocation]] = CSP(layouts, locations)
    csp.add_constraint(LayoutConstraint(layouts))
    
    solution:Optional[Dict[str, List[GridLocation]]] = csp.backtracking_search()
    if solution is None:
        print("답을 찾을 수 없습니다!")
    else:
        for layout, grid_locations in solution.items():
            for index in grid_locations:
                (row, col) = (index.row, index.column)
                grid[row][col] = layout[0]
        display_grid(grid)
```

#### (3)
**제약 충족 문제 해결 프레임워크를 이용하여 스도쿠 문제를 해결할 수 있는 프로그램을 작성하라.**

```python
```





___
## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
