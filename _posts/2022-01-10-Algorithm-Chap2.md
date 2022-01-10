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
뉴클레오타이드는 Enum 타입 대신 비교연산자를 사용할 수 있는 IntEnum 타입을 사용하는데, **이러한 데이터 타입은 구현하려는 검색 알고리즘에서 작동할 수 있어야 한다.**


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
        # 3개의 뉴클레오타이드에서 코돈을 초기화
        codon: Codon = (Nucleotide[s[i]], Nucleotide[s[i + 1]], Nucleotide[s[i + 2]])
        gene.append(codon)  # 코돈을 유전자에 추가
    return gene
    
my_gene: Gene = string_to_gene(gene_str)
```

- `string_to_gene()`함수는 문자열 인자를 취하고, 반복문에서 이를 처리하는 것으로, 문자열 변수를 Gene 타입 앨리어스로 변환하는데 사용한다.
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
print(linear_contains(my_gene, gat))  # 거짓

# 앞에서 정의한 my_gene 변수와 각각의 acg, gat 변수를 함수의 인자로 전달하여 선형 검색
```




#### (3) 이진 검색

**이진 검색(Binary Search)** 은 모든 요소를 살펴보는 *선형 검색보다 빠른 검색 방법*이지만, **해당 자료구조의 저장 순서를 미리 알고 있어야** 한다.
- 자료구조가 정렬되어 있고, 그 인덱스로 항목에 즉시 접근할 수 있는 경우에 이진 검색을 할 수 있다.
- 파이썬에서는 리스트를 사용하여 이진 검색을 수행하는 방식으로 작동한다.
- 이진 검색은 **정렬된 요소들의 범위에서 중간 요소를 검색하여 찾고자 하는 요소와 비교**한다.
- 해당 비교를 기준으로 범위를 반으로 줄이고 다시 이진 검색을 수행하는 방식으로 작동한다.
- 이진 검색은 *검색 공간을 계속해서 절반으로 줄이므로* **최악의 경우 시간복잡도는 `O(lg n)`** 이다.
- 이진 검색은 선형 검색과 달리 정렬된 자료구조가 필요하며, 정렬에는 시간이 소요된다. (최적의 정렬 알고리즘의 시간복잡도는 `O(n lg n)`)

<p> </p>
즉, **검색을 딱 한번 수행**하려 하고 *원본 자료구조가 정렬되어 있지 않다*면, **선형 검색**이 더 효율적이다.

반면에, **검색이 여러 번 수행**된다면, *개별 검색의 시간 비용을 절약*하는 점에서 **이진 검색**이 더 효율적이다.

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
# 무작위로 생성된 값이 sparseness 파라미터의 임곗값보다 더 클 경우 공간은 벽으로 채워진다.
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

`successors() 메서드`는 모든 미로는 크기와 막힌 공간의 비율이 다르기 때문에 미로마다 다르게 움직인다.
`successors() 메서드`는 주어진 미로 공간에서 한 번에 한 칸씩 수평 또는 수직으로 이동할 수 있다는 기준을 사용하여 지정된 위치(MazeLocation)에서 가능한 다음 위치를 찾을 수 있다.
`successors() 메서드`는 **미로에서 상하좌우 위치를 확인하여 해당 위치에서 이동할 수 있는 빈 공간을 찾는다.** 또한, 미로의 가장자리 너머의 위치를 확인하는 것은 피한다. 결론적으로, `successors() 메서드`는 이동 가능한 모든 빈공간(MazeLocation)의 리스트를 반환한다.

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

`Node 클래스`는 **비용(cost)** 과 **휴리스틱(heuristic)** 속성이 있고, `__lt()__` 특수 메서드를 구현하여 정의한다. 
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

**깊이 우선 탐색** 
- `frontier 변수`에서 장소를 방문하면서, 방문한 곳이 목표 지점인지 계속 확인한다.
- 그리고 `successors 변수`에서 현재 지점을 확인하여 다음 이동할 장소를 `frontier 변수`에 추가한다. 
- 또한 이미 방문한 장소를 표시하여 방문한 곳을 다시 방문하지 않도록 한다.
- `frontier 변수`가 비어 있다면 모든 장소를 방문했다는 것을 의미하므로 탐색을 종료한다.


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

**깊이 우선 탐색**은 *같은 방향*으로 계속 탐색하고 막다른 지점과 만났을때 *역추적으로 탐색*한다면, 탐색 전에 더 먼 지점을 탐색하게 될 수 있다. 하지만, **너비 우선 탐색**에서는 한 지점 떨어진 *모든 지점들을 먼저 확인*하고 두 지점, 세 지점과 같이 떨어진 *모든 지점을 목표 지점을 찾을 때까지 계속 탐색*한다. 따라서 **너비 우선 탐색**을 할때는, **최단 경로**를 알 수 있다.


**너비 우선 탐색**을 구현하려면 *선입선출(First-In-First-Out) 원칙에 따라 동작하는 자료구조*인 `큐`에 의존한다.
`큐`는 `스택` 구현과 거의 돌일하나, 다른 점은 `_container 변수`에서 오른쪽 끝 요소 대신 왼쪽 끝 요소를 제거하고 반환하고, 리스트 대신 `덱(deque)`를 사용한다.
```python
class Queue(Generic[T]):
    def __init__(self) -> None:
        self._container: Deque[T] = Deque()

    @property
    def empty(self) -> bool:
        return not self._container  # 컨네이너가 비어있다면 false가 아닌 true

    def push(self, item: T) -> None:
        self._container.append(item)

    def pop(self) -> T:
        return self._container.popleft()  # FIFO

    def __repr__(self) -> str:
        return repr(self._container)
```

`스택`은 오른쪽에서 요소를 추가하고 제거 및 반환을 하고 `큐`는 오른쪽에서 요소를 추가하고 왼쪽에서 요소를 제거 및 반환한다. 

리스트는 오른쪽에서 효율적으로 pop하지만, 왼쪽에서는 그렇지 않다. 

덱은 양쪽에서 효율적으로 pop할 수 있다. `덱`의 왼쪽 pop 시간 복잡도는 `O(1)`이지만 `리스트`의 시간 복잡도는 `O(n)`이다.

```python
def bfs(initial: T, goal_test: Callable[[T], bool], successors: Callable[[T], List[T]]) -> Optional[Node[T]]:
    # frontier : 아직 방문하지 않은 곳
    frontier: Queue[Node[T]] = Queue()
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
            if child in explored:  # 이미 방문한 자식 장소라면 건너뛴다
                continue
            explored.add(child)
            frontier.push(Node(child, current_node))
    return None  # 모든 곳을 방문했지만 결국 목표 지점 찾기 실패
```
**너비 우선 탐색 알고리즘**은 **깊이 우선 탐색 알고리즘**과 동일하며, frontier 변수 타입만 `스택`에서 `큐`로 변경한다. 타입 변경으로 탐색 순서가 변경되고, 출발 지점에서 가장 가까운 지점을 먼저 탐색한다.

`bfs() 함수`를 실행하면 **미로 찾기의 출발 지점에서 목표 지점까지의 최단 경로**를 찾을 수 있다.


#### (5) A* 알고리즘

**A* 알고리즘**은 출발 지점에서 목표 지점까지의 최단 경로를 찾는 것을 목표로, **너비 우선 탐색**과 달리 **A* 알고리즘**은 `비용 함수`와 `휴리스틱 함수`를 사용하여 **목표 지점에 가장 빨리 도달한 가능성이 있는 경로 탐색**에 집중한다.

- 비용 함수 `g(n)` : 특정 지점에 도달하기 위한 비용을 확인, 미로 찾기의 경우 한 지점으로 가기 위해 얼마나 많은 지점을 거쳐야 하는지 확인한다.
- 휴리스틱 함수 `h(n)` : 해당 지점에서 목표 지점까지의 비용을 추정한다. 목표 지점에 도달하는 데 드는 비용을 절대로 과대평가하지 않는다는 *허용 가능한 휴리스틱*이라면, 발견된 경로를 최적으로 판단한다.

탐색을 고려하는 모든 지점에 대한 총 비용은 `f(n)`이다.

`f(n)`은 단순히 `g(n)`과 h(n)을 더한 것으로 `f(n) = g(n) + h(n)`이다.

**A* 알고리즘**은 방문하지 않은 지점에서 다음 지점을 선택할 때 `f(n)`이 가장 낮은 것을 선택하며, 이 부분에서 너비 우선 탐색이나 깊이 우선 탐색 알고리즘과 구별된다.
  

방문하지 않은 지점 중 가장 낮은 `f(n)`의 지점을 선택하기 위해 **A* 알고리즘**은 `우선순위 큐`를 사용한다. 

우선순위 큐의 요소는 내부 요소를 유지한다. 첫 번째 요소는 가장 우선순위가 높은 요소(미로 찾기의 경우, 우선순위가 가장 높은 항목은 f(n)이 가장 낮은 항목)이다.

우선순위 큐는 보통 이진 힙을 내부적으로 사용하며, 푸시와 팝 연삽의 시간복잡도는 `O(lg n)`이다.

파이썬 표준 라이브러리 `heapq 모듈`에서 `heappush()`와 `heappop()`을 제공하고 이 함수는 리스트를 이진 힙으로 유지하며, 이러한 함수를 래핑(Wrapping)하여 `우선순위 큐`를 구현할 수 있다.

특정 요소와 다른 요소의 우선순위를 결정하기 위해 `heappush()`와 `heappop()` 함수에 연산자를 사용하여 비교한다.
```python
# PriorityQue 클래스는 Stack, Queue 클래스의 push(), pop() 메서드에서 각각 heappush(), heappop() 함수를 사용하도록 수정
class PriorityQueue(Generic[T]):
    def __init__(self) -> None:
        self._container: List[T] = []

    @property
    def empty(self) -> bool:
        return not self._container  # 컨테이너가 비어 있다면 not false인 true

    def push(self, item: T) -> None:
        heappush(self._container, item)  # 우선순위 push

    def pop(self) -> T:
        return heappop(self._container)  # 우선순위 pop

    def __repr__(self) -> str:
        return repr(self._container)
```

**휴리스틱**
- 문제를 해결하는 방법을 직관적으로 제시한다. 
- 미로 찾기의 경우 *휴리스틱은 목표 지점에 도달하기 위한 최적 경로 찾기를 목적*으로 한다. 다시 말해 방문하지 않은 지점의 어느 노드가 가장 목표 지점에 가까운지 찾는다. 
- A* 알고리즘에 사용된 휴리스틱은 정확한 *상대 결과를 계산*하고, *허용 가능한 휴리스틱(목표 도달 추정 비용 < 경로에서 현재 지점까지의 최저 가능 비용), 즉 과대평가하지 않는다*면 A* 알고리즘은 *최단 경로*를 제공한다. 
- 더 적은 비용을 계산하는 휴리스틱은 결국 더 많은 지점의 탐색으로 이어진다.  
- 반면 최단 경로에 가까운 휴리스틱은 더 적은 지점의 탐색으로 이어지므로 즉, 허용 가능한 휴리스틱 범위여야 한다. 
- **이상적인 휴리스틱은 지점을 모두 탐색하지 않고 가능한 실제 최단 경로에 가까운 경로를 찾은 것**이다.

**유클리드 거리**
- 두 점 사이의 최단 거리는 직선인 것과 같이, 피타고라스 정리에서 파생된 유클리드 거리는 `sqart((두 점 x 차이)^2 + (두 점 y의 차이)^2)`이다. 
```python
def euclidean_distance(goal: MazeLocation) -> Callable[[MazeLocation], float]:
    def distance(ml: MazeLocation) -> float:
        xdist: int = ml.column - goal.column
        ydist: int = ml.row - goal.row
        return sqrt((xdist * xdist) + (ydist * ydist))
    return distance
```
위 함수는 또 다른 함수를 반환하는데, 파이썬은 `일급 함수(first calss functon)`를 지원하므로 이와 같은 패턴을 사용할 수 있다.
반환된 `distance()` 함수는 `euclidean_distance()` 함수에서 전달받은 `MazeLocation 네임드퓰의 goal 변수`를 `distance()` 함수가 호출될 때마다 `goal 변수`를 참조할 수 있다는 것을 의미하는 **캡처링(capturing)** 이 일어난다.
반환된 distance() 함수는 출발 지점을 인자로 취하고, 목표 지점을 영구적으로 알고 있으며, 목표 지점을 계산한다. 

이 패턴을 사용하면 더 적은 수의 매개 변수를 필요로 하는 함수를 만들 수 있다.


**맨해튼 거리**
- 유클리드 거리가 유용하지만 네 방향 중 한 방향으로만 움직일 수 있다는 미로 찾기의 제한을 고려했을 때, 더 좋은 방법이다. 
- 맨해튼 거리는 뉴욕의 맨해튼 거리를 탐색하는 것에서 유래되었으며, *격자 패턴을 사용*한다. 
- 맨해튼의 어느 한 곳에서 다른 곳으로 가려면 일정 수의 수평 블록과 수직 블록을 걸어야 한다. 
- 맨해튼 거리는 **두 개의 미로 사이의 위치 사이의 행 차이를 찾아서 열 차이와 합산**한다. 
- 맨해튼 거리에는 대각선이 없고, 경로는 수평선 또는 수직선이다.
```python
def manhattan_distance(goal: MazeLocation) -> Callable[[MazeLocation], float]:
    def distance(ml: MazeLocation) -> float:
        xdist: int = abs(ml.column - goal.column)
        ydist: int = abs(ml.row - goal.row)
        return (xdist + ydist)
    return distance
```
맨해튼 거리는 *수평선과 수직선의 거리를 계산*하므로 **미로 찾기 문제**에 더 가깝다. 

따라서 A* 알고리즘에서 **유클리드 거리**를 사용하는 것보다 **맨해튼 거리**를 사용할 때 *더 적은 지점을 탐색*한다.

**너비 우선 탐색에서 A* 알고리즘으로 변경하기 위해서는 몇 가지 작은 수정**이 필요하다.
- `frontier 변수` 타입을 `큐`에서 `우선순위 큐`로 변경
    -> frontier 변수는 가장 낮은 `f(n)`을 갖는 노드를 제거 및 반환한다.
- `explored 변수`의 타입을 `set`에서 `dictionary`로 변경
    -> 방문할 수 있는 각 노드의 최저 비용 `g(n)`을 추적할 수 있다.

휴리스틱 함수를 사용했을 때 노드의 휴리스틱 값이 일치하지 않으면 일부 노드는 두 번 방문될 수 있다. 

`A* 알고리즘`은 새 방향에서 발견된 노드가 이전에 방문한 노드보다 비용이 적은 경우 새 방향의 경로를 더 선호한다.

```python
def astar(initial: T, goal_test: Callable[[T], bool], successors: Callable[[T], List[T]], heuristic: Callable[[T], float]) -> Optional[Node[T]]:
    # frontier : 아직 방문하지 않은 곳 
    frontier: PriorityQueue[Node[T]] = PriorityQueue()
    frontier.push(Node(initial, None, 0.0, heuristic(initial)))
    # explored : 이미 방문한 곳
    explored: Dict[T, float] = {initial: 0.0}

    # 방문할 곳이 더 있는지 탐색
    while not frontier.empty:
        current_node: Node[T] = frontier.pop()
        current_state: T = current_node.state
        # 목표 지점을 찾았다면 종료
        if goal_test(current_state):
            return current_node
        # 방문하지 않은 다음 장소가 있는지 확인
        for child in successors(current_state):
            new_cost: float = current_node.cost + 1  # 현재 장소에서 갈 수 있는 다음 장소의 비용은 1이라고 가정

            if child not in explored or explored[child] > new_cost:
                explored[child] = new_cost
                frontier.push(Node(child, current_node, new_cost, heuristic(child)))
    return None  # 모든 곳을 방문했지만 결국 목표 지점을 찾지 못했다.
```
위에서 작성한 `generic_search.py`는 미로 찾기 문제를 해결할 뿐만 아니라 다양한 검색 애플리케이션에서 사용할 수 있다.

**깊이 우선 탐색**과 **너비 우선 탐색**은 성능이 중요하지 않은 소규모 데이터셋과 공간에 적합하다. 

경우에 따라 *깊이 우선 탐색은 너비 우선 탐색보다 성능이 더 좋을 수 있지*만, **너비 우선 탐색은 항상 최적의 경로를 제공한다는 장점**이 있다. 

흥미롭게도 **너비 우선 탐색**과 **깊이 우선 탐색**의 구현은 **깊이 우선 탐색은 `스택`**을, **너비 우선 탐색은 `큐`** 를 사용하는 것과 같이 **frontier 변수의 타입**을 제외하고 거의 동일하다.

조금 더 복잡한 **A* 알고리즘**은 *일관적이고 허용 가능한 휴리스틱과 결합*되어 **최적의 경로**를 제공할 뿐만 아니라 **너비 우선 탐색보다 성능이 훨씬 좋다**.



`bfs()`와 `astar()` 함수가 모두 최적의 경로를 찾고 있음에도 불구하고 *출력 결과는 서로 다를 수 있는데*, `astar() 함수`는 **휴리스틱** 때문에 *즉시 대각선으로 목표 지점을 향하고* 궁극적으로 `astar() 함수`는 `bfs() 함수`보다 **더 적은 수의 노드를 검색**하므로 성능이 더 좋다.

***

### 2.3 선교사와 식인종 문제
- 세 명의 선교사와 세 명의 식인종이 강 서쪽에 있다.
- 두 명이 탈 수 있는 배를 갖고 있으며, 배를 타고 동쪽으로 이동해야 한다. 
- 강 양쪽에 선교사보다 더 많은 식인종이 있다면, 식인종은 선교사를 잡아먹는다. 
- 강을 건널 때 배에는 적어도 한 명이 탑승해야한다.

#### (1) 문제 나타내기
서쪽 강둑을 추적하여 문제를 나타내면, *서쪽 강둑에는 선교사와 식인종이 각각 몇명이 있고, 배가 서쪽에 있는 지*의 문제만 파악되면 동쪽 강둑에 무엇이 있는지 알 수 있다. 그 이유는 서쪽 강둑에 없는 것은 동쪽 강둑에 있기 때문이다.


```python
from __future__ import annotations
from typing import List, Optional
from generic_search import bfs, Node, node_to_path

MAX_NUM: int = 3

# 서쪽 강둑에 있는 선교사 수와 식인종 수, 배 위치를 초기화하고 현재 상태를 파악하고 결과를 출력하기 위한 메서드가 포함되어 있다
from __future__ import annotations
from typing import List, Optional
from generic_search import bfs, Node, node_to_path

MAX_NUM: int = 3


class MCState:
    def __init__(self, missionaries: int, cannibals: int, boat: bool) -> None:
        self.wm: int = missionaries # 서쪽 강둑에 있는 선교사 수
        self.wc: int = cannibals # 서쪽 강둑에 있는 식인종 수
        self.em: int = MAX_NUM - self.wm  # 동쪽 강둑에 있는 선교사 수 
        self.ec: int = MAX_NUM - self.wc  # 동쪽 강둑에 있는 식인종 수 
        self.boat: bool = boat

    def __str__(self) -> str:
        return ("On the west bank there are {} missionaries and {} cannibals.\n" 
                "On the east bank there are {} missionaries and {} cannibals.\n"
                "The boat is on the {} bank.")\
            .format(self.wm, self.wc, self.em, self.ec, ("west" if self.boat else "east"))
    
    def goal_test(self) -> bool:
        return self.is_legal and self.em == MAX_NUM and self.ec == MAX_NUM
        
    # successors 메서드를 작성하기 위해서는 한 강둑에서 다른 강둑으로 이동 가능한 모든 상태를 확인한 다음 합법적인 상태인지 점검해야 한다
    @property
    def is_legal(self) -> bool:
        if self.wm < self.wc and self.wm > 0:
            return False
        if self.em < self.ec and self.em > 0:
            return False
        return True

    # successors 메서드는 배가 있는 강둑에서 한 명 또는 두 명이 강 건너편으로 이동 가능한 모든 조합을 추가하여 실제로 합법적인 동작을 필터링한다
    def successors(self) -> List[MCState]:
        sucs: List[MCState] = []
        if self.boat: # 서쪽 강둑에 있는 배 
            if self.wm > 1:
                sucs.append(MCState(self.wm - 2, self.wc, not self.boat))
            if self.wm > 0:
                sucs.append(MCState(self.wm - 1, self.wc, not self.boat))
            if self.wc > 1:
                sucs.append(MCState(self.wm, self.wc - 2, not self.boat))
            if self.wc > 0:
                sucs.append(MCState(self.wm, self.wc - 1, not self.boat))
            if (self.wc > 0) and (self.wm > 0):
                sucs.append(MCState(self.wm - 1, self.wc - 1, not self.boat))
        else: # 동쪽 강둑에 있는 배
            if self.em > 1:
                sucs.append(MCState(self.wm + 2, self.wc, not self.boat))
            if self.em > 0:
                sucs.append(MCState(self.wm + 1, self.wc, not self.boat))
            if self.ec > 1:
                sucs.append(MCState(self.wm, self.wc + 2, not self.boat))
            if self.ec > 0:
                sucs.append(MCState(self.wm, self.wc + 1, not self.boat))
            if (self.ec > 0) and (self.em > 0):
                sucs.append(MCState(self.wm + 1, self.wc + 1, not self.boat))
        return [x for x in sucs if x.is_legal]
```

#### (2) 문제 풀이

`bfs()`, `dfs()`, `astar()`로 선교사와 식인종 문제를 풀 때 `node_top_path() 함수`는 궁극적인 솔루션으로 이어지는 상태 리스트로 변환되는 노드를 반환한다.

이러한 상태 리스트를 사람이 쉽게 이해할 수 있도록 출력하는 함수가 필요하므로, 사용한 함수는 `display_solution() 함수`이다.
`display_solution() 함수`는 **사람이 읽을 수 있는 솔루션 경로의 결과를 출력하고 마지막 최근 상태를 추적하면서 솔루션 경로의 모든 상태를 순회**한다. *마지막 상태에서 선교사와 식인종 몇 명이 강을 건넜는지, 배를 타고 어느 방향으로 이동했는지 알아보기 위한 상태를 확인한다.*
```python
def display_solution(path: List[MCState]):
    if len(path) == 0: # 세네티 체크
        return
    old_state: MCState = path[0]
    print(old_state)
    for current_state in path[1:]:
        if current_state.boat:
            print("{} missionaries and {} cannibals moved from the east bank to the west bank.\n"
                  .format(old_state.em - current_state.em, old_state.ec - current_state.ec))
        else:
            print("{} missionaries and {} cannibals moved from the west bank to the east bank.\n"
                  .format(old_state.wm - current_state.wm, old_state.wc - current_state.wc))
        print(current_state)
        old_state = current_state

# 앞에서 탐색 기능을 범용적으로 구현했기 때문에, 재사용하여 bfs()함수 사용
# dfs() 함수를 사용하면 참조적으로 다른 상태를 동일한 값을 표시해야 하기 때문이고, astar() 함수는 휴리스틱을 요구하기 때문에 bfs() 함수 사용
if __name__ == "__main__":
    start: MCState = MCState(MAX_NUM, MAX_NUM, True)
    solution: Optional[Node[MCState]] = bfs(start, MCState.goal_test, MCState.successors)
    if solution is None:
        print("No solution found!")
    else:
        path: List[MCState] = node_to_path(solution)
        display_solution(path)
```

***

### 2.4 적용 사례
- **탐색**은 대부분의 소프트웨어에서 유용하게 사용된다. **검색 기능**이 핵심이고, 그 외 다른 곳에서는 **데이터 저장을 위한 기초**로 사용된다.

- **A* 알고리즘**은 가장 잘 알려진 길 찾기 알고리즘으로, **탐색 공간에서 계산을 미리 수행하는 알고리즘**으로 구현한다. 또한, *단순 맹목적인 검색의 모든 시나리오에서 안정적*이어서 경로 탐색, 프로그래밍 언어의 구문 분석을 위한 최단 경로 찾기 등 *모든 탐색 부분에서 필수 요소*가 되었다. 대부분의 지도 검색 애플리케이션은 **A* 알고리즘의 변형**인 `다익스트라 알고리즘`을 사용하여 탐색한다.

- **너비 우선 탐색**과 **깊이 우선 탐색**은 `균일 비용 탐색(uniform-cost-search)`이나 `역추적 탐색(backtracking search)`같은 복잡한 탐색 알고리즘의 기초가 된다.

- **너비 우선 탐색**은 작은 그래프에서 *최단 경로를 찾는 데 충분한 알고리즘*이지만, 큰 그래프의 경우 훌륭한 *휴리스틱*이 존재한다면 **너비 우선 탐색**에서 **A* 알고리즘**으로 쉽게 변경할 수 있다.

***

### 2.5 연습문제

#### (1)

#### (2)

#### (3)

___
## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
