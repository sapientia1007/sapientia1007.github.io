---
layout: post
title: Algorithm - Chap 4
subtitle: Chap4
categories: Classic_Computer_Science
tags: Algorithm
---

## Chap4 - 그래프 문제

**그래프**는 어떤 한 문제를 연결된 노드 집합으로 구성하며 모델링하는 데 사용하는 추상적인 수학 구조물이다. 

→ 각 노드는 **정점(vertex)** 이라고 부르며, 노드 간의 연결을 **에지(edge)** 라고 한다.

→ 그래프는 문제를 '추상적'으로 만들어주고, 잘 이해할 수 있고 실행 가능한 검색 및 최적화 기술을 적용할 수 있게 해준다.
___

### 4.1 지도와 그래프

이 장에서는 지하철 노선도 대신 미국의 도시와 그 사이의 잠재적인 경로를 사용하여 그래프 문제를 해결한다.

유명한 기업가 엘론 머스크는 진공 튜브 안에서 캡슐 형태의 고속열차가 움직이는 네트워크 시스템을 제안했다.

머스크에 따르면 캡슐은 시속 700마일이고, 900마일 이하로 떨어진 도시 간에 비용 효율적으로 운송할 수 있다고 한다.

이 새로운 운송 시스템을 `하이퍼루프`라고 한다.

이 장에서는 이러한 운송 네트워크를 구축할 때 *고전 그래프 알고리즘*을 사용하여 문제를 해결한다.

머스크는 처음에 로스앤젤레스와 샌프란 시스코를 연결하는 `하이퍼루프`를 제안했다.

**그래프**는 **실제 문제를 그림과 같이 추상적**으로 나타낼 수 있다는 강점이 있다.

이러한 추상화는 미국 지형을 무시하고 도시를 연결하는 맥락에서 하이퍼루프 네트워크에 집중할 수 있다.
___

### 4.2 그래프 프레임워크 구축

파이썬은 다양한 스타일로 코딩할 수 있지만, 내면은 *객체 지향 프로그래밍 언어*이다.

이 절에서는 두 가지 유형의 그래프인 **가중치가 없는 그래프(unweighted graph)** 와 **가중치가 있는 그래프(weighted graph)** 를 정의한다.

이 장 뒷부분에서 설명하는 ***가중치 그래프***는 *각 Edge에 가중치, 즉 숫자 길이를 부여*한 것이다.

객체지향 프로그래밍에서 클래스 계층의 기본인 **상속을 사용**하여 *같은 로직을 중복해서 구현하지 않고 재사용성을 높인다.*

`가중치 클래스`는 `가중치 없는 클래스`의 *하위클래스*로 모델링한다. → 이를 통해 가중치 클래스는 많은 기능을 상속받을 수 있다. 

가중 그래프와 비가중 그래프를 구별할 수 있도록 약간 조정한다. → 가능한 한 많은 다른 문제를 풀 수 있도록 `그래프 프레임워크`를 최대한 유연하게 구현한다.

이 목표를 달성하기 위해 `제네릭`을 사용하여 *정점 타입을 **추상화***한다.

모든 정점에는 정수 인덱스가 지정되지만 사용자 정의 제네릭 타입으로 저장된다.

가장 간단한 Edge 클래스를 정의하여 그래프 프레임워크 구축을 하면 다음과 같다.

```python
from __future__ import annotations
from dataclasses import dataclass

@dataclass
class Edge : 
  u : int # 정점 u에서(from)
  v : int # 정점 v로(to)

  def reversed(self) -> Edge : 
    return Edge(self.v, self.u)

  def __str__(self) -> str :
    return f"{self.u} -> {self.v}"
```
```
** Edge 클래스는 파이썬 3.7의 새로운 기능 datacalss 모듈을 사용한다.
@dataclass 데커레이터로 표시된 클래스는 자동으로 __init__() 특수 메서드를 생성한다.
즉, 타입 어노테이션으로 선언된 변수를 자동으로 인스턴스화한다.
또한, dataclass 모듈은 클래스에서 다른 특수 메서드를 자동으로 생성할 수 있다.
자동으로 생성되는 특수 메서드는 데커레이터를 통해 구성할 수 있다.
간단히 말해, dataclass 모듈은 타이핑을 줄여준다.
```
`Edge 클래스`는 두 정점 사이의 연결로 정의되며, 각 정점은 정수 인덱스로 나타낸다. → 관례상 u는 첫 번째 정점이고, v는 두 번째 정점을 나타낸다.

이 장에서는 `방향이 없는 그래프(무향 그래프)`, 즉 **Edge가 양방향으로 이동 가능한 그래프**를 사용한다.

`방향이 있는 그래프(유향 그래프, digraph)`는 **Edge가 단방향**일 수 있다.

`Graph 클래스`는 **정점(노드)** 과 **에지**를 연결하는 그래프의 핵심 역할에 중점을 둔다.

다시 말해, 그래프 프레임워크 사용자가 원하는 대로 실제 정점 타입을 지정할 수 있어야 한다.

이는 모든 타입을 허용하는 중간 자료구조를 만들 필요 없이 프레임워크를 다양한 문제에 사용할 수 있게 해준다.

```python
from typing import TypeVar, Generic, List, Optional
from edge import Edge

V = TypeVar('V') # 그래프 정점(vertice) 타입

class Graph(Generic[V]) : 
  def __init__(self, vertices : List[V] = []) -> None :
    self.vertices : List[V] = vertices
    self._edges : List[List[Edge]] = [[] for _ in vertices]
```
리스트 변수 `_vertices`는 `Graph 클래스`의 핵심이다.

각 정점은 리스트에 저장되고, 리스트의 정수 인덱스를 참조한다.

정점은 복잡한 데이터 타입일 수도 있지만, 인덱스는 항상 정수다.


그래프 자료구조를 구현하는 방법은 여러 가지가 있지만, 가장 일반적인 두 방법은 **정점 행렬**과 **인접 리스트**를 사용하는 것이다.

정점 행렬에서 행렬의 각 셀은 그래프에서 두 정점의 교차점을 나타내며, 셀 값은 두 정점 사이의 연결을 나타낸다.

여기서는 그래프 자료구조로 인접 리스트를 사용한다.

인접 리스트에서 모든 정점에는 그것이 연결된 모든 정점 리스트가 있다.

구체적으로 에지에 대한 리스트의 리스트를 사용한다.

즉, 모든 정점에 대한 한 정점이 다른 정점에 연결되는 에지 리스트가 있는데, 이것은 리스트의 리스트 변수인 `_edges`다.

```python
from typing import TypeVar, Generic, List, Optional
from edge import Edge

V = TypeVar('V') # 그래프 정점(vertice) 타입

class Graph(Generic[V]) : 
  def __init__(self, vertices : List[V] = []) -> None :
    self.vertices : List[V] = vertices
    self._edges : List[List[Edge]] = [[] for _ in vertices]

  @property
  def vertex_count(self) -> str :
    return len(self._vertices) # 정점 수
  
  @property
  def edge_count(self) -> str :
    return sum(map(len, self._edges)) # 에지 수

  # 그래프에 정점을 추가하고 인덱스를 반환한다.
  def add_vertex(self, vertex : V) -> int :
    self._vertices.append(vertex)
    self._edges.append([]) # 에지에 빈 리스트를 추가한다.
    return self.vertex_count - 1 # 추가된 정점의 인덱스를 반환한다.

  # 무향 그래프이므로 항상 양방향으로 에지를 추가한다.
  def add_edge(self, edge : Edge) -> None :
    self._edges[edge.u].append(edge)
    self._edges[edge.v].append(edge.reversed())

  # 정점 인덱스를 사용하여 에지를 추가한다.(헬퍼 메서드)
  def add_edge_by_indices(self, u: int, v: int) -> None:
        edge: Edge = Edge(u, v)
        self.add_edge(edge)

  # 정점 인덱스를 참조하여 에지를 추가한다.(헬퍼 메서드)
  def add_edge_by_vertices(self, first: V, second: V) -> None:
      u: int = self._vertices.index(first)
      v: int = self._vertices.index(second)
      self.add_edge_by_indices(u, v)

  # 특정 인덱스 정점을 찾는다.
  def vertex_at(self, index: int) -> V:
      return self._vertices[index]

  # 정점 인덱스를 찾는다.
  def index_of(self, vertex: V) -> int:
      return self._vertices.index(vertex)

  # 정점 인덱스에 연결된 이웃 정점을 찾는다. -> 정점의 이웃을 반환
  def neighbors_for_index(self, index: int) -> List[V]:
      return list(map(self.vertex_at, [e.v for e in self._edges[index]]))

  # 정점의 이웃 정점을 찾는다.(헬퍼 메서드)
  def neighbors_for_vertex(self, vertex: V) -> List[V]:
      return self.neighbors_for_index(self.index_of(vertex))

  # 정점 인덱스에 연결된 모든 에지를 반환한다.
  def edges_for_index(self, index: int) -> List[Edge]:
      return self._edges[index]

  # 정점의 해당 에지를 반환한다.
  def edges_for_vertex(self, vertex: V) -> List[Edge]:
      return self.edges_for_index(self.index_of(vertex))

  # 그래프 출력
  def __str__(self) -> str:
      desc: str = ""
      for i in range(self.vertex_count):
          desc += f"{self.vertex_at(i)} -> {self.neighbors_for_index(i)}\n"
      return desc


if __name__ == "__main__" : 
  # 기본 그래프 구축 테스트
  city_graph : Graph[str] = Graph(["시애틀", "샌프란시스코", "로스앤젤레스", 
  "리버사이드", "피닉스", "시카고", "보스턴", "뉴욕", "애틀랜타", "마이애미", "댈러스",
  "휴스턴", "디트로이트", "필라델피아", "워싱턴"])
  city_graph.add_edge_by_vertices("시애틀", "시카고")
  city_graph.add_edge_by_vertices("시애틀", "샌프란시스코")
  city_graph.add_edge_by_vertices("샌프란시스코", "리버사이드")
  city_graph.add_edge_by_vertices("샌프란시스코", "로스앤젤리스")
  city_graph.add_edge_by_vertices("로스앤젤리스", "리버사이드")
  city_graph.add_edge_by_vertices("로스앤젤리스", "피닉스")
  city_graph.add_edge_by_vertices("리버사이드", "피닉스")
  city_graph.add_edge_by_vertices("리버사이드", "시카고")
  city_graph.add_edge_by_vertices("피닉스", "댈러스")
  city_graph.add_edge_by_vertices("피닉스", "휴스턴")
  city_graph.add_edge_by_vertices("댈러스", "시카고")
  city_graph.add_edge_by_vertices("댈러스", "애틀랜타")
  city_graph.add_edge_by_vertices("댈러스", "휴스턴")
  city_graph.add_edge_by_vertices("휴스턴", "애틀랜타")
  city_graph.add_edge_by_vertices("휴스턴", "마이애미")
  city_graph.add_edge_by_vertices("애틀랜타", "시카고")
  city_graph.add_edge_by_vertices("애틀랜타", "워싱턴")
  city_graph.add_edge_by_vertices("애틀랜타", "마이애미")
  city_graph.add_edge_by_vertices("마이애미", "워싱턴")
  city_graph.add_edge_by_vertices("시카고", "디트로이트")
  city_graph.add_edge_by_vertices("디트로이트", "보스턴")
  city_graph.add_edge_by_vertices("디트로이트", "워싱턴")
  city_graph.add_edge_by_vertices("디트로이트", "뉴욕")
  city_graph.add_edge_by_vertices("보스턴", "뉴욕")
  city_graph.add_edge_by_vertices("뉴욕", "필라델피아")
  city_graph.add_edge_by_vertices("필라델피아", "워싱턴")
  print(city_graph)
```
위 클래스에서 대부분의 메서드는 인덱스와 정점에 대해 각각의 메서드가 따로 존재한다.

클래스 정의에서 리스트 `_vertices`는 모든 타입을 허용하는 `V 타입 요소`의 리스트다.

그리고 리스트 `_vertices`에 저장되는 `V 타입`의 정점이 있다.

나중에 정점을 검색하거나 조작하는 경우 해당 리스트에 저장된 위치를 알아야 한다.

따라서 **모든 정점에는 리스트와 연관된 인덱스**가 있다.

정점의 인덱스를 모르는 경우 리스트 `_vertices`를 검색해서 찾아야 한다. 이것이 정수 인덱스와 V 타입에 대한 각각의 메서드가 존재하는 이유이다.

이전에 언급한 것처럼, 이 장에서는 `무향 그래프`만 사용한다.

그래프에는 **방향을 지정하거나 가중치를 적용**할 수 있다.

가중치 그래프는 각 *에지가 서로 비교 가능한 값을 갖는 그래프*다.

`하이퍼루프 네트워크`의 가중치를 역 간의 거리로 생각할 수 있다.

즉, 구현된 `Edge`와 `Graph 클래스`를 사용하여 `하이퍼루프 네트워크`를 만들 수 있다.

또한, 제네릭으로 정점의 타입을 문자열로 지정한다는 것은 문자열 타입은 타입 변수 V에 적용된다는 것이다.
___

### 4.3 최단 경로 찾기

`하이퍼루프`는 매우 빠르다.

한 역에서 다른 역으로의 이동 시간을 최적화하기 위해서는 *역 간의 거리가 얼마나 먼지보다는* **한 역에서 다른 역으로 이동하는 데 거치는 역의 수**가 더 중요하다.

그래프 이론에서 두 정점을 연결하는 에지의 집합을 **경로**라고 한다.

경로는 *하나의 정점에서 다른 정점으로 이동하는 길이*이다.

`하이퍼루프 네트워크`에서 어느 한 에지의 집합은 **한 정점에서 다른 정점까지의 경로**를 나타낸다.

정점 간의 최단 경로 찾기는 그래프가 사용되는 일반적인 문제 중 하나다.


**가중치가 없는 그래프에서 *최단 경로*를 찾는 것**은 *시작 지점과 목표 지점 사이의 에지가 가장 적은 경로*를 찾는 것이다.

`하이퍼루프 네트워크`를 구축하려면 먼저 인구 밀도가 높은 해안가의 가장 먼 도시를 연결하는 게 좋다.

*그래프의 최단 경로를 찾는 알고리즘*은 2장에서 구현한 것을 재사용할 수 있는데, **너비 우선 탐색**은 미로찾기와 마찬가지로 *그래프에서도 사용*할 수 있다.

*정점은 미로의 위치이며, 에지는 한 위치에서 다른 위치로 갈 수 있는 길이다.*

**가중치가 없는 그래프**에서 `너비 우선 탐색`은 두 정점 사이의 최단 경로를 찾는다.


```python
# city_graph 변수에 2장의 너비 우선 탐색을 재사용
import sys
# 상위 디렉터리에 있는 2장 패키지에 접근
sys.path.insert(0, '..')
from ch2.generic_serach import bfs, Node, node_to_path
# bfs 함수는 시작 지점, 목표지점, 현재 지점에서 다음 지점을 찾기 위한 변수로 3개의 매개변수를 취한다.

# 시작 지점 : 보스턴 / 목표 지점 : 마이애미 / 정점이 마이애미와 같은지 확인
bfs_result : Optional[Node[V]] = bfs("보스턴", lambda x : x == "마이애미", 
city_graph.neighbors_for_vertex)
if bfs_result is None :
  print("너비 우선 탐색으로 답을 찾을 수 없습니다!")
else :
  path : List[V] = node_to_path(bfs_result)
  print("보스턴에서 마이애미까지의 최단 경로 :")
  print(path)
```

보스턴에서 디트로이드, 워싱턴을 거쳐 마이애미까지 최단 경로에 대한 에지수는 3이다.
___

### 4.4 네트워크 구축 비용 최소화

미국의 15개 대도시 통계 구역을 `하이퍼루프 네트워크`에 연결한다고 생각해보면, 목표는 *네트워크를 구축하는 비용을 최소화*하는 것이다. 즉, 최소화의 노선을 설치하는 것이다.

특정한 에지가 요구하는 *노선의 양*을 이해하려면 **에지가 나타내는 거리**를 알아야 한다. 여기서 **가중치**를 사용한다.

`하이퍼루프 네트워크`에서 **에지의 가중치**는 연결된 두 대도시 사이의 거리이다.

가중치를 처리하려면 `Edge`의 서브클래스인 `WeightedEdge`와 `Graph`의 서브클래스인 `WeightedGraph`가 필요하다.

모든 `WeightedEdge` 클래스에는 가중치를 나타내는 부동소수점 값이 있다.

`프림 알고리즘(야르니트 알고리즘)`에서 **한 에지를 다른 에지와 비교하여 가장 작은 가중치를 가진 에지를 찾는 함수가 필요**하다.

```python
# weighted_edge.py
from __future__ import annotations
from dataclasses import dataclass
from edge import Edge

@dataclass
class WeightedEdge(Edge):
    weight: float

    def reversed(self) -> WeightedEdge:
        return WeightedEdge(self.v, self.u, self.weight)

    # 가장 작은 가중치를 가진 에지를 찾기 위해 에지를 가중치 순으로 정렬
    def __lt__(self, other: WeightedEdge) -> bool:
        return self.weight < other.weight

    def __str__(self) -> str:
        return f"{self.u} {self.weight}> {self.v}"
```

```python
# weighted_graph.py
from typing import TypeVar, Generic, List, Tuple
from graph import Graph
from weighted_edge import WeightedEdge

V = TypeVar('V') 


class WeightedGraph(Generic[V], Graph[V]):
    def __init__(self, vertices: List[V] = []) -> None:
        self._vertices: List[V] = vertices
        self._edges: List[List[WeightedEdge]] = [[] for _ in vertices]

    def add_edge_by_indices(self, u: int, v: int, weight: float) -> None:
        edge: WeightedEdge = WeightedEdge(u, v, weight)
        self.add_edge(edge) # 슈퍼클래스 메서드 호출

    def add_edge_by_vertices(self, first: V, second: V, weight: float) -> None:
        u: int = self._vertices.index(first)
        v: int = self._vertices.index(second)
        self.add_edge_by_indices(u, v, weight)

    def neighbors_for_index_with_weights(self, index: int) -> List[Tuple[V, float]]:
        distance_tuples: List[Tuple[V, float]] = []
        for edge in self.edges_for_index(index):
            distance_tuples.append((self.vertex_at(edge.v), edge.weight))
        return distance_tuples

    def __str__(self) -> str:
        desc: str = ""
        for i in range(self.vertex_count):
            desc += f"{self.vertex_at(i)} -> {self.neighbors_for_index_with_weights(i)}\n"
        return desc


if __name__ == "__main__" : 
  city_graph2 : WeightedGraph[str] = WeightedGraph(["시애틀", "샌프란시스코", "로스앤젤레스", 
  "리버사이드", "피닉스", "시카고", "보스턴", "뉴욕", "애틀랜타", "마이애미", "댈러스",
  "휴스턴", "디트로이트", "필라델피아", "워싱턴"])
  city_graph2.add_edge_by_vertices("시애틀", "시카고", 1737)
  city_graph2.add_edge_by_vertices("시애틀", "샌프란시스코", 678)
  city_graph2.add_edge_by_vertices("샌프란시스코", "리버사이드", 386)
  city_graph2.add_edge_by_vertices("샌프란시스코", "로스앤젤리스", 348)
  city_graph2.add_edge_by_vertices("로스앤젤리스", "리버사이드", 50)
  city_graph2.add_edge_by_vertices("로스앤젤리스", "피닉스", 357)
  city_graph2.add_edge_by_vertices("리버사이드", "피닉스", 307)
  city_graph2.add_edge_by_vertices("리버사이드", "시카고", 1704)
  city_graph2.add_edge_by_vertices("피닉스", "댈러스", 887)
  city_graph2.add_edge_by_vertices("피닉스", "휴스턴", 1015)
  city_graph2.add_edge_by_vertices("댈러스", "시카고", 805)
  city_graph2.add_edge_by_vertices("댈러스", "애틀랜타", 721)
  city_graph2.add_edge_by_vertices("댈러스", "휴스턴", 225)
  city_graph2.add_edge_by_vertices("휴스턴", "애틀랜타", 702)
  city_graph2.add_edge_by_vertices("휴스턴", "마이애미", 968)
  city_graph2.add_edge_by_vertices("애틀랜타", "시카고", 588)
  city_graph2.add_edge_by_vertices("애틀랜타", "워싱턴", 543)
  city_graph2.add_edge_by_vertices("애틀랜타", "마이애미", 604)
  city_graph2.add_edge_by_vertices("마이애미", "워싱턴", 923)
  city_graph2.add_edge_by_vertices("시카고", "디트로이트", 238)
  city_graph2.add_edge_by_vertices("디트로이트", "보스턴", 613)
  city_graph2.add_edge_by_vertices("디트로이트", "워싱턴", 396)
  city_graph2.add_edge_by_vertices("디트로이트", "뉴욕", 482)
  city_graph2.add_edge_by_vertices("보스턴", "뉴욕", 190)
  city_graph2.add_edge_by_vertices("뉴욕", "필라델피아", 81)
  city_graph2.add_edge_by_vertices("필라델피아", "워싱턴", 123)
  print(city_graph2)
```


**트리**는 *두 정점 사이에 한 방향의 경로만 존재하는 그래프*의 일종이다.

이것은 트리에는 **사이클이 없다는 것(비순환적)** 을 의미한다.

그래프의 한 시작점에서 같은 에지를 반복하지 않고 다시 같은 시작점으로 돌아갈 수 있다면 사이클이 있다는 것을 의미한다.

그래프에는 에지 가지치기를 통해 트리가 될 수 있다.

**연결된 그래프**는 한 정점에서 다른 정점으로 가는 몇가지 방법이 있다.

**신장 트리**는 그래프의 모든 정점을 연결하는 트리, **최소 신장 트리**는 가중치 그래프의 모든 정점을 다른 신장 트리와 비교했을 때 최소 비용으로 연결한 트리이다.

최소 신장 트리를 찾는다는 것은 가중치 그래프의 모든 정점에서 최소 가중치와 연결하는 방법을 찾는 것을 의미한다.

```python
# priority_queue.py
from typing import TypeVar, Generic, List
from heapq import heappush, heappop

T = TypeVar('T')

class PriorityQueue(Generic[T]):
    def __init__(self) -> None:
        self._container: List[T] = []

    @property
    def empty(self) -> bool:
        return not self._container  # 컨테이너가 비었다면 false가 아닌 True

    def push(self, item: T) -> None:
        heappush(self._container, item)  # 우선순위 push

    def pop(self) -> T:
        return heappop(self._container)  # 우선순위 pop

    def __repr__(self) -> str:
        return repr(self._container)
```

**최소 신장 트리**를 구현하기 전에 *총 가중치를 계산하는 함수*를 구현하면, 최소 신장 트리 문제에 대한 결과는 **트리를 구성하는 에지 리스트**다.

먼저, `WeightedPath 타입 앨리어스`를 `WeightedEdge 클래스 리스트`로 정의한다.

그리고 `WeightedPath 타입 앨리어스`를 매개변수로 갖고, 모든 에지의 가중치를 합한 총 무게를 갖는 `total_weight() 함수`를 정의한다.

**최소 신장 트리**를 찾기 위한 `프림 알고리즘`은 그래프를 두 부분으로 나눈다.

즉, 최소 신장 트리를 찾는 과정에서 **최소 신장 트리에 포함된 정점**과 **포함되지 않은 정점**으로 나눈다.

**`프림 알고리즘`의 수행 과정**

1. 최소 신장 트리에 포함할 한 정점을 정한다.
2. 아직 최소 신장 트리에 포함하지 않은 정점 중에서 정점에 연결된 가장 낮은 가중치 에지를 찾는다.
3. 가장 낮은 가중치 에지의 정점을 최소 신장 트리에 추가한다.
4. 그래프의 모든 정점이 최소 신장 트리에 추가될 때까지 2와 3을 반복한다.

`프림 알고리즘`은 `우선순위 큐`를 사용한다.

새 정점이 최소 신장 트리에 추가될 때마다 트리 외부 정점에 연결되는 모든 출력 에지가 **우선순위 큐에 추가**된다.

**최소 가중치 에지**는 항상 *우선순위 큐에서 pop*되며, 알고리즘은 우선순위 큐가 빌 때까지 계속 실행된다.

그러므로 **최소 가중치 에지**는 항상 트리에 먼저 추가되고, 이미 트리의 정점에 연결된 에지는 pop할 때 무시된다.

```
** 프림 알고리즘은 야르니크 알고리즘이라고도 한다. 
프림알고리즘은 방향이 있는 그래프에서 제대로 작동하지 않는다.
또한, 연결되지 않은 그래프에서는 아예 작동하지 않는다.
```

```python
# mst.py
from typing import TypeVar, List, Optional
from weighted_graph import WeightedGraph
from weighted_edge import WeightedEdge
from priority_queue import PriorityQueue

V = TypeVar('V') 
WeightedPath = List[WeightedEdge] 


def total_weight(wp: WeightedPath) -> float:
    return sum([e.weight for e in wp])


def mst(wg: WeightedGraph[V], start: int = 0) -> Optional[WeightedPath]:
    if start > (wg.vertex_count - 1) or start < 0:
        return None
    result: WeightedPath = [] # 최소 신장 트리 결과
    pq: PriorityQueue[WeightedEdge] = PriorityQueue()
    visited: List[bool] = [False] * wg.vertex_count # 방문한 곳

    def visit(index: int):
        visited[index] = True # 방문한 곳으로 표시
        for edge in wg.edges_for_index(index):
            # 해당 정점의 모든 에지를 우선순위 큐에 추가
            if not visited[edge.v]:
                pq.push(edge)

    visit(start) # 첫 번째 정점에서 모든 게 시작

    while not pq.empty: # 우선순위 큐에 에지가 남아 있을 때까지 계속 반복
        edge = pq.pop()
        if visited[edge.v]:
            continue # 방문한 곳이면 넘어간다
        # 최소 가중치 에지를 결과에 추가
        result.append(edge)
        visit(edge.v) # 연결된 에지를 방문

    return result


def print_weighted_path(wg: WeightedGraph, wp: WeightedPath) -> None:
    for edge in wp:
        print(f"{wg.vertex_at(edge.u)} {edge.weight}> {wg.vertex_at(edge.v)}")
    print(f"가중치 총합: {total_weight(wp)}")

if __name__ == "__main__" : 
  city_graph2 : WeightedGraph[str] = WeightedGraph(["시애틀", "샌프란시스코", "로스앤젤레스", 
  "리버사이드", "피닉스", "시카고", "보스턴", "뉴욕", "애틀랜타", "마이애미", "댈러스",
  "휴스턴", "디트로이트", "필라델피아", "워싱턴"])
  city_graph2.add_edge_by_vertices("시애틀", "시카고", 1737)
  city_graph2.add_edge_by_vertices("시애틀", "샌프란시스코", 678)
  city_graph2.add_edge_by_vertices("샌프란시스코", "리버사이드", 386)
  city_graph2.add_edge_by_vertices("샌프란시스코", "로스앤젤리스", 348)
  city_graph2.add_edge_by_vertices("로스앤젤리스", "리버사이드", 50)
  city_graph2.add_edge_by_vertices("로스앤젤리스", "피닉스", 357)
  city_graph2.add_edge_by_vertices("리버사이드", "피닉스", 307)
  city_graph2.add_edge_by_vertices("리버사이드", "시카고", 1704)
  city_graph2.add_edge_by_vertices("피닉스", "댈러스", 887)
  city_graph2.add_edge_by_vertices("피닉스", "휴스턴", 1015)
  city_graph2.add_edge_by_vertices("댈러스", "시카고", 805)
  city_graph2.add_edge_by_vertices("댈러스", "애틀랜타", 721)
  city_graph2.add_edge_by_vertices("댈러스", "휴스턴", 225)
  city_graph2.add_edge_by_vertices("휴스턴", "애틀랜타", 702)
  city_graph2.add_edge_by_vertices("휴스턴", "마이애미", 968)
  city_graph2.add_edge_by_vertices("애틀랜타", "시카고", 588)
  city_graph2.add_edge_by_vertices("애틀랜타", "워싱턴", 543)
  city_graph2.add_edge_by_vertices("애틀랜타", "마이애미", 604)
  city_graph2.add_edge_by_vertices("마이애미", "워싱턴", 923)
  city_graph2.add_edge_by_vertices("시카고", "디트로이트", 238)
  city_graph2.add_edge_by_vertices("디트로이트", "보스턴", 613)
  city_graph2.add_edge_by_vertices("디트로이트", "워싱턴", 396)
  city_graph2.add_edge_by_vertices("디트로이트", "뉴욕", 482)
  city_graph2.add_edge_by_vertices("보스턴", "뉴욕", 190)
  city_graph2.add_edge_by_vertices("뉴욕", "필라델피아", 81)
  city_graph2.add_edge_by_vertices("필라델피아", "워싱턴", 123)
  print(city_graph2)
```

- `mst()함수`는 최소 신장 트리를 표현하는 `WeightedPath 타입 앨리어스`에서 선택된 경로를 반환한다.
- `result 변수`는 최소 신장 트리의 가중치 경로를 저장한다.
- **최소 가중치의 에지**를 pop 하여 이를 추가하고, *그래프의 다른 정점으로 이동*한다.
- `프림 알고리즘`은 항상 최소 가중치의 에지를 선택하기 때문에 `탐욕 알고리즘`이다.
- `pq 변수`는 발견된 새로운 에지를 저장하고, 그다음 낮은 가중치 에지를 pop한다.
- `visited 변수`는 이미 방문한 정점 인덱스를 추적한다.
- 그래프가 연결되어 있지 않다면, 어떤 정점이 먼저 방문되어는지는 중요하지 않다. 
- 만약 그래프가 연결되어 있진 않지만, 독립적으로 연결된 그래프 **컴포넌트**로 구성되어 있다면 `mst() 함수`는 시작 정점에 속하는 특정 컴포넌트의 트리를 반환한다.
- 우선순위 큐에 에지가 남아 있다면 에지를 pop하고, 아직 신장 트리에 없는 정점으로 연결되는지 확인한다.
- 우선순위 큐는 오름차순이기 때문에, 가장 낮은 가중치 에지가 먼저 pop된다. → 이는 결과가 최소 가중치임을 보장한다.
___

### 4.5 가중치 그래프에서 최단 경로 찾기

`하이퍼루프 네트워크`미국 는 전역의 도시를 한꺼번에 연결할 수 없다.

대신 주요 도시 간을 방문하는 데 드는 비용을 최소화할 수 있다.

`다익스트라 알고리즘`은 **최단 경로 찾기 문제**를 해결한다.

가중치 그래프에서 *시작점*과 다른 *모든 정점*에 대한 **최소 가중치 경로**를 반환한다.

`다익스트라 알고리즘`은 단일 소스 정점에서 시작하여 가장 가까운 정점을 계속 탐색한다.

이러한 특성 때문에 `다익스트라 알고리즘`은 `프림 알고리즘`과 같이 탐욕적이다.

`다익스트라 알고리즘`에서 새로운 정점을 탐색했을 때, 시작 정점으로부터 얼마나 멀리 떨어져 있는지 추적한다.

만약 이 정점에 대한 더 짧은 경로를 찾았다면, 값을 갱신한다.

**`다익스트라 알고리즘`의 과정**

1. 시작 정점을 우선순위 큐에 추가한다.
2. 우선순위 큐에서 가장 가까운 정점을 pop한다.
3. 현재 정점에 연결된 모든 이웃 정점을 확인한다. 이웃 정점이 기록되지 않았거나 에지가 새로운 최단 경로일 경우 시작점에서 각 이웃 정점의 거리와 에지를 기록한다. 그리고 해당 정점을 우선순위 큐에 추가한다.
4. 우선순위 큐가 빌 때까지 2, 3 단계를 반복한다.
5. 시작점에서 다른 모든 정점까지의 최단 거리를 반환한다.


```python
from __future__ import annotations
from typing import TypeVar, List, Optional, Tuple, Dict
from dataclasses import dataclass
from mst import WeightedPath, print_weighted_path
from weighted_graph import WeightedGraph
from weighted_edge import WeightedEdge
from priority_queue import PriorityQueue

V = TypeVar('V') # 그래프의 정점 타입


@dataclass
class DijkstraNode:
    vertex: int
    distance: float

    def __lt__(self, other: DijkstraNode) -> bool:
        return self.distance < other.distance

    def __eq__(self, other: DijkstraNode) -> bool:
        return self.distance == other.distance


def dijkstra(wg: WeightedGraph[V], root: V) -> Tuple[List[Optional[float]], Dict[int, WeightedEdge]]:
    first: int = wg.index_of(root) # 시작 인덱스를 찾는다.
    # 처음에는 거리를 알 수 없다.
    distances: List[Optional[float]] = [None] * wg.vertex_count
    distances[first] = 0 # 루트에서 루트 자신의 거리는 0이다
    path_dict: Dict[int, WeightedEdge] = {} # 정점에 대한 경로
    pq: PriorityQueue[DijkstraNode] = PriorityQueue()
    pq.push(DijkstraNode(first, 0))

    while not pq.empty: # 우선순위 큐가 빌 때까지 다익스트라 알고리즘 
        u: int = pq.pop().vertex # 다음 가까운 정점을 탐색한다.
        dist_u: float = distances[u] # 이 정점에 대한 거리를 이미 알고 있다.
        # 이 정점에서 모든 에지 및 정점을 살펴본다.
        for we in wg.edges_for_index(u):
            # 이 정점에 대한 이전 거리
            dist_v: float = distances[we.v]
            # 이전 거리가 없거나 혹은 최단 경로가 존재한다면,
            if dist_v is None or dist_v > we.weight + dist_u:
                # 정점의 거리를 갱신
                distances[we.v] = we.weight + dist_u
                # 정점의 최단 경로 에지를 갱신
                path_dict[we.v] = we
                # 해당 정점을 나중에 곧 탐색
                pq.push(DijkstraNode(we.v, we.weight + dist_u))

    return distances, path_dict


def distance_array_to_vertex_dict(wg: WeightedGraph[V], distances: List[Optional[float]]) -> Dict[V, Optional[float]]:
    distance_dict: Dict[V, Optional[float]] = {}
    for i in range(len(distances)):
        distance_dict[wg.vertex_at(i)] = distances[i]
    return distance_dict


# 에지의 딕셔너리 인자를 취해 각 노드에 접근하고, 정점 start에서 end까지 가는 에지 리스트를 반환
def path_dict_to_path(start: int, end: int, path_dict: Dict[int, WeightedEdge]) -> WeightedPath:
    if len(path_dict) == 0:
        return []
    edge_path: WeightedPath = []
    e: WeightedEdge = path_dict[end]
    edge_path.append(e)
    while e.u != start:
        e = path_dict[e.u]
        edge_path.append(e)
    return list(reversed(edge_path))



if __name__ == "__main__" : 
  city_graph2 : WeightedGraph[str] = WeightedGraph(["시애틀", "샌프란시스코", "로스앤젤레스", 
  "리버사이드", "피닉스", "시카고", "보스턴", "뉴욕", "애틀랜타", "마이애미", "댈러스",
  "휴스턴", "디트로이트", "필라델피아", "워싱턴"])
  city_graph2.add_edge_by_vertices("시애틀", "시카고", 1737)
  city_graph2.add_edge_by_vertices("시애틀", "샌프란시스코", 678)
  city_graph2.add_edge_by_vertices("샌프란시스코", "리버사이드", 386)
  city_graph2.add_edge_by_vertices("샌프란시스코", "로스앤젤리스", 348)
  city_graph2.add_edge_by_vertices("로스앤젤리스", "리버사이드", 50)
  city_graph2.add_edge_by_vertices("로스앤젤리스", "피닉스", 357)
  city_graph2.add_edge_by_vertices("리버사이드", "피닉스", 307)
  city_graph2.add_edge_by_vertices("리버사이드", "시카고", 1704)
  city_graph2.add_edge_by_vertices("피닉스", "댈러스", 887)
  city_graph2.add_edge_by_vertices("피닉스", "휴스턴", 1015)
  city_graph2.add_edge_by_vertices("댈러스", "시카고", 805)
  city_graph2.add_edge_by_vertices("댈러스", "애틀랜타", 721)
  city_graph2.add_edge_by_vertices("댈러스", "휴스턴", 225)
  city_graph2.add_edge_by_vertices("휴스턴", "애틀랜타", 702)
  city_graph2.add_edge_by_vertices("휴스턴", "마이애미", 968)
  city_graph2.add_edge_by_vertices("애틀랜타", "시카고", 588)
  city_graph2.add_edge_by_vertices("애틀랜타", "워싱턴", 543)
  city_graph2.add_edge_by_vertices("애틀랜타", "마이애미", 604)
  city_graph2.add_edge_by_vertices("마이애미", "워싱턴", 923)
  city_graph2.add_edge_by_vertices("시카고", "디트로이트", 238)
  city_graph2.add_edge_by_vertices("디트로이트", "보스턴", 613)
  city_graph2.add_edge_by_vertices("디트로이트", "워싱턴", 396)
  city_graph2.add_edge_by_vertices("디트로이트", "뉴욕", 482)
  city_graph2.add_edge_by_vertices("보스턴", "뉴욕", 190)
  city_graph2.add_edge_by_vertices("뉴욕", "필라델피아", 81)
  city_graph2.add_edge_by_vertices("필라델피아", "워싱턴", 123)
  print(city_graph2)
```

`dijkstra() 함수`의 처음 몇 줄은 `distances 변수`를 제외하고 친숙해진 자료구조를 사용한다.

`distances 변수`는 **시작점에서 모든 정점까지의 거리**를 나타낸다.

처음에는 정점들이 얼마나 멀리 떨어져 있는지 모르기 때문에, 이 거리가 모두 초기화가 되어있고, 이것을 알아내기 위해 `다익스트라 알고리즘`을 사용한다.

함수 `dijkstra()`는 가중치 그래프 루트 정점에서 **모든 정점까지의 거리와 최단 경로**를 풀 수 있는 `path_dict 딕셔너리`를 반환한다.

**`다익스트라 알고리즘`이 `프림 알고리즘`과 닮았다는 것을 확인할 수 있다.**

둘 다 *탐욕 알고리즘*이고, 비슷한 코드를 사용하여 구현할 수 있다.

또한, `다익스트라 알고리즘`은 `A * 알고리즘`과 닮았다.

`다익스트라 알고리즘`에서 *단일 대상을 찾는다고 제한하고 휴리스틱을 추가*하면, `A * 알고리즘`과 **동일**하다.
___

### 4.6 적용 사례

그래프를 사용하여 세계 곳곳을 표현할 수 있고, 다양한 네트워크에서도 그래프를 사용할 수 있다.

그래프 알고리즘은 통신, 해상 운송, 수송 및 유틸리티 산업의 효율성을 위한 필수적인 알고리즘이다.

소매업은 복잡한 유통 문제를 처리해야하는데, 상점과 창고를 정점으로 생각할 수 있으며, 그 사이의 거리를 에지로 생각할 수 있다. 

인터넷은 각 연결된 장치가 정점이고, 각 유선 혹은 무선 연결이 에지인 거대한 그래프이다.

어떤 사업 분야에서든 연료나 배선을 절약하기 위해 최소 신장 트리와 최단 경로를 유용하게 사용할 수 있다.

그래프 알고리즘의 일부 명백한 적용사례는 소셜 네트워크 및 지도 애플리케이션이다. 소셜 네트워크에서 사람은 정점이며, 연결은 에지다.
___

### 4.7 연습 문제

#### (1)
**그래프 프레임워크에 에지 및 정점 제거를 위한 메서드를 추가하라**
```python
from typing import TypeVar, Generic, List, Optional
from edge import Edge


V = TypeVar('V') 


class Graph(Generic[V]):
    ...

    def remove_vertex(self, vertex : V) -> None : # 정점 제거
        u = self.index_of(vertex)
        for i in self.neighbors_for_index(u):
            v = self.index_of(i)
            edge : Edge = Edge(u,v)
            self._edges[edge.u].remove(edge)
            self._edges[edge.v].remove(edge.reversed())

    def remove_edges(self, vertex1: V, vertex2: V) -> List[V] : # 간선 제거
        u = self.index_of(vertex1)
        v = self.index_of(vertex2)
        edge : Edge = Edge(u, v)
        self._edges[edge.u].remove(edge)
        self._edges[edge.v].remove(edge.reversed())      
       
    def __str__(self) -> str:
        desc: str = ""
        for i in range(self.vertex_count):
            desc += f"{self.vertex_at(i)} -> {self.neighbors_for_index(i)}\n"
        return desc

    
if __name__ == "__main__" : 
  # 기본 그래프 구축 테스트
  city_graph : Graph[str] = Graph(["시애틀", "샌프란시스코", "로스앤젤리스", 
  "리버사이드", "피닉스", "시카고", "보스턴", "뉴욕", "애틀랜타", "마이애미", "댈러스",
  "휴스턴", "디트로이트", "필라델피아", "워싱턴"])
  city_graph.add_edge_by_vertices("시애틀", "시카고")
  city_graph.add_edge_by_vertices("시애틀", "샌프란시스코")
  city_graph.add_edge_by_vertices("샌프란시스코", "리버사이드")
  city_graph.add_edge_by_vertices("샌프란시스코", "로스앤젤리스")
  city_graph.add_edge_by_vertices("로스앤젤리스", "리버사이드")
  city_graph.add_edge_by_vertices("로스앤젤리스", "피닉스")
  city_graph.add_edge_by_vertices("리버사이드", "피닉스")
  city_graph.add_edge_by_vertices("리버사이드", "시카고")
  city_graph.add_edge_by_vertices("피닉스", "댈러스")
  city_graph.add_edge_by_vertices("피닉스", "휴스턴")
  city_graph.add_edge_by_vertices("댈러스", "시카고")
  city_graph.add_edge_by_vertices("댈러스", "애틀랜타")
  city_graph.add_edge_by_vertices("댈러스", "휴스턴")
  city_graph.add_edge_by_vertices("휴스턴", "애틀랜타")
  city_graph.add_edge_by_vertices("휴스턴", "마이애미")
  city_graph.add_edge_by_vertices("애틀랜타", "시카고")
  city_graph.add_edge_by_vertices("애틀랜타", "워싱턴")
  city_graph.add_edge_by_vertices("애틀랜타", "마이애미")
  city_graph.add_edge_by_vertices("마이애미", "워싱턴")
  city_graph.add_edge_by_vertices("시카고", "디트로이트")
  city_graph.add_edge_by_vertices("디트로이트", "보스턴")
  city_graph.add_edge_by_vertices("디트로이트", "워싱턴")
  city_graph.add_edge_by_vertices("디트로이트", "뉴욕")
  city_graph.add_edge_by_vertices("보스턴", "뉴욕")
  city_graph.add_edge_by_vertices("뉴욕", "필라델피아")
  city_graph.add_edge_by_vertices("필라델피아", "워싱턴")
  city_graph.remove_edges("필라델피아", "뉴욕") # '필라델피아 <-> 뉴욕' 간선 제거
  print(city_graph)
```
```
시애틀 -> ['시카고', '샌프란시스코']
샌프란시스코 -> ['시애틀', '리버사이드', '로스앤젤리스']
로스앤젤리스 -> ['샌프란시스코', '리버사이드', '피닉스']
리버사이드 -> ['샌프란시스코', '로스앤젤리스', '피닉스', '시카고']
피닉스 -> ['로스앤젤리스', '리버사이드', '댈러스', '휴스턴']
시카고 -> ['시애틀', '리버사이드', '댈러스', '애틀랜타', '디트로이트']
보스턴 -> ['디트로이트', '뉴욕']
뉴욕 -> ['디트로이트', '보스턴']
애틀랜타 -> ['댈러스', '휴스턴', '시카고', '워싱턴', '마이애미']
마이애미 -> ['휴스턴', '애틀랜타', '워싱턴']
댈러스 -> ['피닉스', '시카고', '애틀랜타', '휴스턴']
휴스턴 -> ['피닉스', '댈러스', '애틀랜타', '마이애미']
디트로이트 -> ['시카고', '보스턴', '워싱턴', '뉴욕']
필라델피아 -> ['워싱턴']
워싱턴 -> ['애틀랜타', '마이애미', '디트로이트', '필라델피아']
```
```python
city_graph.remove_vertex("보스턴") # '보스턴' 정점 제거
print(city_graph)
```
```
시애틀 -> ['시카고', '샌프란시스코']
샌프란시스코 -> ['시애틀', '리버사이드', '로스앤젤리스']
로스앤젤리스 -> ['샌프란시스코', '리버사이드', '피닉스']
리버사이드 -> ['샌프란시스코', '로스앤젤리스', '피닉스', '시카고']
피닉스 -> ['로스앤젤리스', '리버사이드', '댈러스', '휴스턴']
시카고 -> ['시애틀', '리버사이드', '댈러스', '애틀랜타', '디트로이트']
보스턴 -> []
뉴욕 -> ['디트로이트']
애틀랜타 -> ['댈러스', '휴스턴', '시카고', '워싱턴', '마이애미']
마이애미 -> ['휴스턴', '애틀랜타', '워싱턴']
댈러스 -> ['피닉스', '시카고', '애틀랜타', '휴스턴']
휴스턴 -> ['피닉스', '댈러스', '애틀랜타', '마이애미']
디트로이트 -> ['시카고', '워싱턴', '뉴욕']
필라델피아 -> ['워싱턴']
워싱턴 -> ['애틀랜타', '마이애미', '디트로이트', '필라델피아']
```
```
정점을 제거하기 위해서는, 그 정점에 연결된 모든 간선들을 지우고, 해당 정점의 간선 집합은 비우게 작성했다.
간선을 제거하기 위해서는, 임의의 정점 두개를 연결한 간선들을 지우도록 작성했다.
```


#### (2)
**그래프 프레임워크에 유향 그래프(digraph)를 사용할 수 있도록 코드를 추가하라**

```python
from typing import TypeVar, Generic, List, Optional
from edge import Edge


V = TypeVar('V') 


class Graph(Generic[V]):
    ...
    
    def add_edge_digraph(self, edge: Edge) -> None :
        self._edges[edge.u].append(edge)
    
    def add_edge_by_indices_digraph(self, u: int, v: int) -> None:
        edge: Edge = Edge(u, v)
        self.add_edge_digraph(edge)

    def add_edge_by_vertices_digraph(self, first: V, second: V) -> None:
        u: int = self._vertices.index(first)
        v: int = self._vertices.index(second)
        self.add_edge_by_indices_digraph(u, v)
        
if __name__ == "__main__" : 
  city_graph : Graph[str] = Graph(["시애틀", "샌프란시스코", "로스앤젤리스", 
  "리버사이드", "피닉스", "시카고"])
  city_graph.add_edge_by_vertices("시애틀", "시카고") # 무향
  city_graph.add_edge_by_vertices_digraph("시애틀", "샌프란시스코") # 유향
  city_graph.add_edge_by_vertices("샌프란시스코", "리버사이드") # 무향
  city_graph.add_edge_by_vertices_digraph("샌프란시스코", "로스앤젤리스") #유향
  city_graph.add_edge_by_vertices("로스앤젤리스", "리버사이드") # 무향
  city_graph.add_edge_by_vertices("로스앤젤리스", "피닉스") # 무향
  city_graph.add_edge_by_vertices_digraph("리버사이드", "피닉스") # 유향
  city_graph.add_edge_by_vertices_digraph("리버사이드", "시카고") # 유향 

  print(city_graph)
```
```
시애틀 -> ['시카고', '샌프란시스코']
샌프란시스코 -> ['리버사이드', '로스앤젤리스']
로스앤젤리스 -> ['리버사이드', '피닉스']
리버사이드 -> ['샌프란시스코', '로스앤젤리스', '피닉스', '시카고']
피닉스 -> ['로스앤젤리스']
시카고 -> ['시애틀']
```
```
유향 그래프는 정점 간의 연결선이 방향을 갖고 있는, 즉 에지가 단방향인 그래프를 이야기한다.
유향 그래프를 사용할 수 있도록, 에지를 추가할때 한 방향으로만 저장하고, 반대 방향은 저장하지 않도록 작성했다.
```


#### (3)
**그래프 프레임워크를 사용하여 위키피디아 설명되어 있는 것과 같은 [쾨니히스베르크 다리 건너기 문제](https://namu.wiki/w/%EC%BE%A8%EB%8B%88%ED%9E%88%EC%8A%A4%EB%B2%A0%EB%A5%B4%ED%81%AC%20%EB%8B%A4%EB%A6%AC%20%EA%B1%B4%EB%84%88%EA%B8%B0%20%EB%AC%B8%EC%A0%9C)를 증명 또는 반증하라**

![..](http://jjhcom.github.io/assets/images/banners/bridge_fin.jpg) : <https://blog.naver.com/falcon2026/221237421277>

```python
from typing import TypeVar, Generic, List, Optional
from edge import Edge
from random import choice

V = TypeVar('V') 

class Graph(Generic[V]):
  ...
  
if __name__ == "__main__" :

  def remove_bridge(u : int, v : int) :
      edge : Edge = Edge(u, v)
      city_graph._edges[edge.u].remove(edge)
      city_graph._edges[edge.v].remove(edge.reversed())
  def show_bridge(u : int, v: int) :
      remove_bridge(u, v)
      print(f"<<{city_graph._vertices[edge.u]} -> {city_graph._vertices[edge.v]}>>")
      print(city_graph)
      
    
  city_graph : Graph[str] = Graph(["A","B","C","D"])
  city_graph.add_edge_by_vertices("A","B")
  city_graph.add_edge_by_vertices("A","B")
  city_graph.add_edge_by_vertices("A","D")
  city_graph.add_edge_by_vertices("D","B")
  city_graph.add_edge_by_vertices("D","C")
  city_graph.add_edge_by_vertices("A","C")
  city_graph.add_edge_by_vertices("A","C")
  print(city_graph)
  
  print("------------------------")
  edge = choice(choice(city_graph._edges))
  show_bridge(edge.u, edge.v)

  while bool(city_graph._edges) :   
      try : 
          edge = choice(city_graph._edges[edge.v])
          show_bridge(edge.u, edge.v)

      except :
          print("결과가 나오지 않아요")
          break
```
```
A -> ['B', 'B', 'D', 'C', 'C']
B -> ['A', 'A', 'D']
C -> ['D', 'A', 'A']
D -> ['A', 'B', 'C']

------------------------
<<C -> A>>
A -> ['B', 'B', 'D', 'C']
B -> ['A', 'A', 'D']
C -> ['D', 'A']
D -> ['A', 'B', 'C']

<<A -> D>>
A -> ['B', 'B', 'C']
B -> ['A', 'A', 'D']
C -> ['D', 'A']
D -> ['B', 'C']

<<D -> C>>
A -> ['B', 'B', 'C']
B -> ['A', 'A', 'D']
C -> ['A']
D -> ['B']

<<C -> A>>
A -> ['B', 'B']
B -> ['A', 'A', 'D']
C -> []
D -> ['B']

<<A -> B>>
A -> ['B']
B -> ['A', 'D']
C -> []
D -> ['B']

<<B -> D>>
A -> ['B']
B -> ['A']
C -> []
D -> []

결과가 나오지 않아요
```
```
나무위키에서 보았듯이, 임의의 지점에서 출발하여 일곱 개의 다리를 한 번씩만 건너서 원래 위치로 돌아오도록 알고리즘을 작성했을때, 
어떻게 그어도 다리 하나가 항상 건너지 못한 채로 남아 있게 된다는 것을 확인할 수 있다.
--> 개선할 점 : 하나의 에지 집합만 다 비워, 프로그램이 자동 종료된다. 
```
___
## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
