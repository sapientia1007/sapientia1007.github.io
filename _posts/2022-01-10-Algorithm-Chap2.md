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
- 선형 검색은 찾고자 하는 요소가 발견되거나 자료구조의 끝에 도달할 때까지 순서대로 모든 요소를 훑어본다.
- 선형 검색은 가장 간단하고, 자연스럽고, 명백한 방법으로 검색한다.
- **최악**의 경우 선형 검색은 *자료구조의 모든 요소를 거쳐야* 하므로 **최악의 시간복잡도는 `O(n)`** 이다. (n : 자료구조의 요소 수)
- 단순히 자료구조의 모든 요소를 탐색하면서 탐색할 항목과 동등한지 확인한다.

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

미로의 개별 위치를 나타내는 방법이 필요하고, 여기에서 행과 열 속성을 가진 네임드튜플(NamedTuple)을 사용한다.
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

```python
# 미로를 생성할 때 막힌 공간의 무작위 비율을 설정하기 위해 sparseness 매개변수의 기본값 20%
class Maze:
    def __init__(self, rows: int = 10, columns: int = 10, sparseness: float = 0.2, start: MazeLocation = MazeLocation(0, 0), goal: MazeLocation = MazeLocation(9, 9)) -> None:
        # 기본 인스턴스 변수 초기화
        self._rows: int = rows
        self._columns: int = columns
        self.start: MazeLocation = start
        self.goal: MazeLocation = goal
        # 격자를 빈 공간으로 채운다
        self._grid: List[List[Cell]] = [[Cell.EMPTY for c in range(columns)] for r in range(rows)]
        # 격자에 막힌 공간을 무작위로 채운다
        self._randomly_fill(rows, columns, sparseness)
        # 시작 위치와 목표 위치를 설정한다
        self._grid[start.row][start.column] = Cell.START
        self._grid[goal.row][goal.column] = Cell.GOAL

    def _randomly_fill(self, rows: int, columns: int, sparseness: float):
        for row in range(rows):
            for column in range(columns):
                if random.uniform(0, 1.0) < sparseness:
                    self._grid[row][column] = Cell.BLOCKED

    # 미로 출력
    def __str__(self) -> str:
        output: str = ""
        for row in self._grid:
            output += "".join([c.value for c in row]) + "\n"
        return output

    def goal_test(self, ml: MazeLocation) -> bool:
        return ml == self.goal

    def successors(self, ml: MazeLocation) -> List[MazeLocation]:
        locations: List[MazeLocation] = []
        if ml.row + 1 < self._rows and self._grid[ml.row + 1][ml.column] != Cell.BLOCKED:
            locations.append(MazeLocation(ml.row + 1, ml.column))
        if ml.row - 1 >= 0 and self._grid[ml.row - 1][ml.column] != Cell.BLOCKED:
            locations.append(MazeLocation(ml.row - 1, ml.column))
        if ml.column + 1 < self._columns and self._grid[ml.row][ml.column + 1] != Cell.BLOCKED:
            locations.append(MazeLocation(ml.row, ml.column + 1))
        if ml.column - 1 >= 0 and self._grid[ml.row][ml.column - 1] != Cell.BLOCKED:
            locations.append(MazeLocation(ml.row, ml.column - 1))
        return locations

    def mark(self, path: List[MazeLocation]):
        for maze_location in path:
            self._grid[maze_location.row][maze_location.column] = Cell.PATH
        self._grid[self.start.row][self.start.column] = Cell.START
        self._grid[self.goal.row][self.goal.column] = Cell.GOAL
    
    def clear(self, path: List[MazeLocation]):
        for maze_location in path:
            self._grid[maze_location.row][maze_location.column] = Cell.EMPTY
        self._grid[self.start.row][self.start.column] = Cell.START
        self._grid[self.goal.row][self.goal.column] = Cell.GOAL
  
def euclidean_distance(goal: MazeLocation) -> Callable[[MazeLocation], float]:
    def distance(ml: MazeLocation) -> float:
        xdist: int = ml.column - goal.column
        ydist: int = ml.row - goal.row
        return sqrt((xdist * xdist) + (ydist * ydist))
    return distance


def manhattan_distance(goal: MazeLocation) -> Callable[[MazeLocation], float]:
    def distance(ml: MazeLocation) -> float:
        xdist: int = abs(ml.column - goal.column)
        ydist: int = abs(ml.row - goal.row)
        return (xdist + ydist)
    return distance  
  
if __name__ == "__main__":
    # Test DFS
    m: Maze = Maze()
    print(m)
    solution1: Optional[Node[MazeLocation]] = dfs(m.start, m.goal_test, m.successors)
    if solution1 is None:
        print("No solution found using depth-first search!")
    else:
        path1: List[MazeLocation] = node_to_path(solution1)
        m.mark(path1)
        print(m)
        m.clear(path1)
    # Test BFS
    solution2: Optional[Node[MazeLocation]] = bfs(m.start, m.goal_test, m.successors)
    if solution2 is None:
        print("No solution found using breadth-first search!")
    else:
        path2: List[MazeLocation] = node_to_path(solution2)
        m.mark(path2)
        print(m)
        m.clear(path2)
    # Test A*
    distance: Callable[[MazeLocation], float] = manhattan_distance(m.goal)
    solution3: Optional[Node[MazeLocation]] = astar(m.start, m.goal_test, m.successors, distance)
    if solution3 is None:
        print("No solution found using A*!")
    else:
        path3: List[MazeLocation] = node_to_path(solution3)
        m.mark(path3)
        print(m)
```
`Maze 클래스`는 상태를 나타내는 격자를 내부적으로 추적한다. 그리고 행 수, 열 수, 시작 위치 및 목표 위치에 대한 인스턴스 변수를 가지고 있다. 격자에는 막힌 공간이 무작위로 채워진다.

#### (2) 기타 미로 세부사항

**미로를 찾는 동안 목표 지점에 도달했는지 여부를 확인**하는 기능이 있어야 하는데, 검색된 특정 위치(MazeLocation 네임드튜플)가 목표 지점인지 확인해야 하므로, `goal_test 함수`를 작성한다.

`successors() 메서드`는 **미로에서 상하좌우 위치를 확인하여 해당 위치에서 이동할 수 있는 빈 공간을 찾는다.** 또한, 미로의 가장자리 너머의 위치를 확인하는 것은 피한다. `successors() 메서드`는 이동 가능한 모든 빈공간(MazeLocation)의 리스트를 반환한다.

#### (3) 깊이 우선 탐색

**깊이 우선 탐색(Depth-First Serach)** 는 이름에서 알 수 있듯이 **막다른 지점에 도달하여 최종 결정 지점으로 되돌아오기 전까지 가능한 깊이 탐색**한다. 미로 찾기 문제에서는 다른 문제에도 재사용할 수 있도록 일반적인 깊이 우선 탐색을 구현할 것이다.


**깊이 우선 탐색 알고리즘**은 *후입선출(Last-In-First-Out) 원칙에 따라 동작하는 자료구조*인 `스택`에 의존한다.

**스택**
- 후입선출(Last-In-First-Out) 원칙에 따라 동작하는 자료구조
- `push()` 작업은 스택 상단에 항목을 추가한다.
- `pop()` 작업은 스택 상단의 항목을 제거하고, 반환한다.

즉, 파이썬 리스트를 사용하여 스택을 구현할 때 오른쪽 끝에서 항목을 **추가**하고, **제거 및 반환**을 한다. 리스트에 항목이 없으면 pop()메서드는 실패하므로 스택에서도 실패한다.

**깊이 우선 탐색**을 구현하기 전에 노드를 구현해야 하는데, 탐색은 한 장소에서 다른 장소의 변화를 추적하기 위해 `Node 클래스`가 필요하다.
미로 찾기에서 노드는 장소를 감싼 래퍼(Wrapper)로 생각할 수 있고, 장소는 MazeLocation 타입이다.

`Node 클래스`는 **비용(cost)** 과 **휴리스틱(heuristic)** 속성이 있고, `__lt()` 특수 메서드를 구현하여 정의한다. 
```python
class Node(Generic[T]):
    def __init__(self, state: T, parent: Optional[Node], cost: float = 0.0, heuristic: float = 0.0) -> None:
        self.state: T = state
        self.parent: Optional[Node] = parent
        self.cost: float = cost
        self.heuristic: float = heuristic

    def __lt__(self, other: Node) -> bool:
        return (self.cost + self.heuristic) < (other.cost + other.heuristic)
```

**깊이 우선 탐색**은 다음 두 자료구조를 추적한다.
- 탐색 방문하려고 하는 장소 스택으로 다음 코드에서 `frontier 변수`로 표현한다.
- 이미 방문한 장소 셋으로 `explored 변수`로 표현한다.

**깊이 우선 탐색**은 `frontier 변수`에서 장소를 방문하면서, 문한 곳이 목표 지점인지 계속 확인한다.

그리고 `successors 변수`에서 현재 지점을 확인하여 다음 이동할 장소를 `frontier 변수`에 추가한다. 

또한 이미 방문한 장소를 표시하여 방문한 곳을 다시 방문하지 않도록 한다.

`frontier 변수`가 비어 있다면 모든 장소를 방문했다는 것을 의미하므로 탐색을 종료한다.


```python
# 목표 지점을 찾았다면 목표 지점 경로를 캡슐화한 노드를 반환
# 출발 지점부터 목표 지점까지의 경로는 이 노드의 parent 속성을 사용하여 노드를 반전함으로써 재구성

def dfs(initial: T, goal_test: Callable[[T], bool], successors: Callable[[T], List[T]]) -> Optional[Node[T]]:
    # frontier : 아직 방문하지 않은 곳
    frontier: Stack[Node[T]] = Stack()
    frontier.push(Node(initial, None))
    # explored : 이미 방문한 곳
    explored: Set[T] = {initial}

    # 방문할 곳이 더 있는지 탐색
    while not frontier.empty:
        current_node: Node[T] = frontier.pop()
        current_state: T = current_node.state
        # 목표 지점을 찾았다면 종료
        if goal_test(current_state):
            return current_node
        # 방문하지 않은 다음 장소가 있는지 확인
        for child in successors(current_state):
            if child in explored:  # 이미 방문한 장소라면 건너뛴다
                continue
            explored.add(child)
            frontier.push(Node(child, current_node))
    return None  # 모든 곳을 방문했지만 결국 목표 지점 찾기 실패


# 미로의 출발 지점, 목표 지점, 경로를 출력
# 같은 미로에서 다른 탐색 알고리즘을 시도할 수 있도록 경로를 초기화
def node_to_path(node: Node[T]) -> List[T]:
    path: List[T] = [node.state]
    # 노드 경로 반전
    while node.parent is not None:
        node = node.parent
        path.append(node.state)
    path.reverse()
    return path
```



#### (4) 너비 우선 탐색

깊이 우선 탐색으로 찾은 목표 지점에 대한 경로는 부자연스럽게 보일 수 있으며, 최단 경로가 아닐 수 있다.

**너비 우선 탐색(Breadth-First Search)** 은 탐색의 각 **반복마다 출발 지점에서 한 계층의 노드를 가까운 지점부터 순차적으로 탐색**함으로써 항상 최단 경로를 찾는다. 

**깊이 우선 탐색**은 일반적으로 **너비 우선 탐색**보다 더 빨리 목표 지점을 찾지만, 그 반대의 경우도 있어서 두 가지 방법 중 하나를 선택하는 것은 *최단 경로를 선택하느냐 빠른 탐색의 가능성을 선택하느냐*의 문제이다.

깊이 우선 탐색과 같이 같은 방향으로 계속 탐색하고 막다른 
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
