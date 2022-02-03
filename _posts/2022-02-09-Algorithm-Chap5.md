---
layout: post
title: Algorithm - Chap 5
subtitle: Chap5
categories: Classic_Computer_Science
tags: Algorithm
use_math: true
---

## Chap5 - 유전 알고리즘

**유전 알고리즘**은 일반적으로 쉬운 해결책이 없으며, 복잡한 문제를 위한 알고리즘이다.

하나의 흥미로운 예는 **단백질-리간드 결합(protein-ligad docking)** 과 **약물 설계**이다.

전산 생물학자들은 약물을 전달하기 위해 수용채에 결합할 분자를 설계한다.

특정 분자를 설계하기 위한 명백한 알고리즘이 존재하지 않지만, 유전 알고리즘은 때때로 많은 지침 없이 문제 정의를 넘어선 답을 제공한다.

## 

### 5.1 생물학적 배경

생물학에서 **진화론**은 환경의 제약과 함께 유전적 돌연변이가 시간이 지남에 따라 새로운 종을 창조하는 **종형성**을 포함하여 **생물체의 변화를 어떻게 이끌어내는지**에 대한 설명이다.

*잘 적응된 유기체는 성공하고 덜 적응된 유기체는 실패하는 메커니즘*을 **자연 선택(natural selection)** 이라고 한다.

종의 각 세대에는 *유전적 돌연변이*를 통해 나타나는 *다양한 특성을 가진 개체가 포함*되고, 모든 개체는 *생존을 위해 제한된 자원에 대해 경쟁*하며, 자원보다 많은 개체가 있으면 *일부 개체는 죽어야 한다.*

그러한 환경에서 **생존에 더 잘 적응하는 돌연변이가 있는 개체는 살아남고, 번식할 가능성이 더 높다.**

시간이 지남에 따라 더 잘 적응된 개체는 더 많은 자식을 낳고, 상속을 통해 돌연변이를 자식에게 물려주면서 생존에 도움이 되는 돌연변이가 집단에서 확산된다.


**자연 선택**은 생물학적 영역을 넘어 모든 곳에 적용되었고, **사회진화론**은 사회 이론 영역에 적용되는 자연 선택이다. 컴퓨터 과학에서 유전 알고리즘은 계산 문제를 해결하기 위한 자연 선택의 시뮬레이션이다.


**유전 알고리즘**은 *염색체*로 알려진 개체의 **모집단(population, group)** 을 포함한다.

각자의 특성을 나타내는 유전자로 구성된 염색체는 어떤 문제를 해결하기 위해 경쟁하고 있다. 

염색체가 문제를 얼마나 잘 해결하는지는 **적합도 함수(fitness functon)** 에 의해 정의된다.

유전 알고리즘은 **세대**를 거지쳐 진행된다. 

세대마다 적합한 염색체는 재생산될 때 선택될 가능성이 더 크다.

또한 세대마다 두 개의 염색체가 유전자를 합칠 가능성이 있는데, 이를 **크로스오버(crossover)** 라고 한다.

그리고 세대마다 염색체의 유전자가 변이될 가능성이 있다.

모집단의 일부 개체의 적합도 함수에서 지정된 임계값을 초과하거나 알고리즘이 지정된 최대 세대 수를 통과한다면, 가장 적합한 개체가 반환된다.

유전 알고리즘은 모든 문제에 대한 좋은 해결책이 아니다. 부분적으로 또는 완전히 확률론적으로 결정된 세 가지 조작(선택, 크로스오버, 돌연변이)에 의존한다. 

따라서 합리적인 시간 내에 최적의 솔루션을 찾지 못할 수 있고, 대부분의 문제는 확실한 알고리즘이 존재하지만, 빠른 결정론적 알고리즘이 존재하지 않는 문제가 있으므로 **유전 알고리즘**을 사용하여 해결할 수 있다.

___

### 5.2 제네릭 유전 알고리즘

**유전 알고리즘**은 종종 특정 애플리케이션에 대해 매우 특수화되고 조율된다. 이 장에서는 여러 가지 문제에 사용할 수 있는 **제네릭 유전 알고리즘(일반 유전 알고리즘)** 을 정의한다.

반면 특수한 상황에서는 잘 맞지 않을 수 있으므로, 이를 위한 몇 가지 설정 가능한 옵션이 포함되지만, 이 장의 목표는 이러한 옵션 대신 알고리즘 기본사항을 잘 나타내는 것이다.


먼저 제네릭 알고리즘이 작동할 수 있는 개체에 대한 인터페이스를 정의한다.

`Chromosome 추상 클래스`는 네 가지 필수 기능을 정의하고, `염색체 클래스`는 아래 기능을 수행한다.
- 자체 적합도를 결정한다.
- 첫 세대를 채워서 사용하기 위해 무작위로 선택된 유전자로 인스턴스를 생성한다.
- 자식을 생성하기 위해 같은 타입의 유형을 다른 타입과 결합하는 크로스오버를 구현하여 다른 염색체와 혼합한다.
- 돌연변이를 구현하여 자체로 작고 무작위적인 변화를 만든다.

```python
# chromosome.py

from __future__ import annotations
from typing import TypeVar, Tuple, Type
from abc import ABC, abstractmethod

# TypeVar 클래스의 T가 Chromosome 클래스와 바인딩되어 있기 때문에, 타입이 T인 변수는 Chromosome 클래스의 인스턴스 혹은 서브클래스여야 한다.
T = TypeVar('T', bound = 'Chromosome') # 자신을 반환하기 위해서 사용

# 모든 염색체의 베이스 클래스, 모든 메서드는 오버라이드 된다.
class Chromosome(ABC) :
  @abstractmethod
  def fitness(self) -> float :
    ...

  @classmethod
  @abstractmethod
  def random_instance(cls : Type[T]) -> T :
    ...

  @abstractmethod
  def crossover(self : T, other : T) -> Tuple[T, T] :
    ...

  @abstractmethod 
  def mutate(self) -> None :
    ...
```

**유전 알고리즘**이 취하는 단계
  1. 유전 알고리즘 1세대에 대한 무작위 염색체 초기 모집단을 생성한다.
  2. 1세대 집단에서 각 염색체의 적합도를 측정하고, 임곗값을 초과하는 염색체가 있다면 이를 반환하고 알고리즘을 종료한다.
  3. 개체의 재생산을 위해 가장 높은 적합도를 가진 확률이 높은 개체를 선택한다.
  4. 다음 세대 집단을 나타내는 자식을 생성하기 위해 일정 확률로 선택한 염색체를 크로스오버(결합)한다.
  5. 낮은 확률로 일부 염색체를 돌연변이시켜, 새로운 세대 집단이 완성되었으며, 이것은 마지막 세대의 집단을 대체한다.
  6. 알고리즘이 지정한 세대의 최댓값에 도달하지 않은 경우 과정2로 돌아가고, 최댓값에 도달했다면 적합도가 가장 높은 염색체를 반환한다.


유전 알고리즘 개요에 대한 내용을 `GeneticAlgorithm 클래스`로 구현하면 다음과 같다.
```python
# genetic_algorithm.py

from __future__ import annotations
from typing import TypeVar, List, Tuple, Callable, Generic
from enum import Enum
from random import choices, random
from heapq import nlargest
from statistics import mean
from chromosome import Chromosome

C = TypeVar('C', bound = Chromosome) # 염색체 타입

class GeneticAlgorithm(Generic[C]) : 
  SelectionType = Enum("SelectionType", "ROULETTE TOURNAMENT")

  def __init__(self, initial_population: List[C], threshold: float, max_generations: int = 100, mutation_chance: float = 0.01, crossover_chance: float = 0.7, selection_type: SelectionType = SelectionType.TOURNAMENT) -> None:
      self._population: List[C] = initial_population
      self._threshold: float = threshold
      self._max_generations: int = max_generations
      self._mutation_chance: float = mutation_chance
      self._crossover_chance: float = crossover_chance
      self._selection_type: GeneticAlgorithm.SelectionType = selection_type
      self._fitness_key: Callable = type(self._population[0]).fitness

  # 두 부모를 선택하기 위해 룰렛휠(확률 분포)을 사용한다.
  # 메모 : 음수 적합도와 작동하지 않는다.
  def _pick_roulette(self, wheel: List[float]) -> Tuple[C, C]:
      return tuple(choices(self._population, weights=wheel, k=2))

  # 무작위로 num_participants만큼 추출한 후 적합도가 가장 높은 두 염색체를 선택한다.
  def _pick_tournament(self, num_participants: int) -> Tuple[C, C]:
      participants: List[C] = choices(self._population, k=num_participants)
      return tuple(nlargest(2, participants, key=self._fitness_key))

  # 집단을 새로운 세대로 교체한다.
  def _reproduce_and_replace(self) -> None:
      new_population: List[C] = []
      # 새로운 세대가 채워질 때까지 반복
      while len(new_population) < len(self._population):
          # parents 중 두 부모 선택
          if self._selection_type == GeneticAlgorithm.SelectionType.ROULETTE:
              parents: Tuple[C, C] = self._pick_roulette([x.fitness() for x in self._population])
          else:
              parents = self._pick_tournament(len(self._population) // 2)
          # 두 부모를 크로스오버
          if random() < self._crossover_chance:
              new_population.extend(parents[0].crossover(parents[1]))
          else:
              new_population.extend(parents)
      # 새 집단의 수가 홀수라면, 이전 집단보다 하나 더 많으므로 제거
      if len(new_population) > len(self._population):
          new_population.pop()
      self._population = new_population # 새 집단으로 참조를 변경

  # _mutation_chance 확률로 각 개별 염색체를 돌연변이
  def _mutate(self) -> None:
      for individual in self._population:
          if random() < self._mutation_chance:
              individual.mutate()

  # max_generations만큼 유전 알고리즘을 실행하고, 최상의 적합도를 가진 개체를 반환
  def run(self) -> C:
      best: C = max(self._population, key=self._fitness_key)
      for generation in range(self._max_generations):
          # 임곗값을 초과하면 개체를 바로 반환
          if best.fitness() >= self._threshold:
              return best
          print(f"세대 {generation} 최상 {best.fitness()} 평균 {mean(map(self._fitness_key, self._population))}")
          self._reproduce_and_replace()
          self._mutate()
          highest: C = max(self._population, key=self._fitness_key)
          if highest.fitness() > best.fitness():
              best = highest # 새로운 최상의 개체 발견
      return best # _max_generations에서 발견한 최상의 개체를 반환
```

___

### 5.3 간단한 방정식

유전 알고리즘의 `GeneticAlgorithm 제네릭 클래스`는 `Chromosome 클래스`를 구현하는 모든 타입에서 작동한다.

$ 6x - x^2 + 4y - y^2 $ 이 최대가 되는 $x$와 $y$는 무엇일까?

**미적분**을 사용하여 **편미분**을 취하고 각각을 0으로 설정하면 값을 최대로하는 $x$와 $y$를 구할 수 있다.

결과는 $ x = 3 $, $ y = 2 $이다. 

미적분을 사용하지 않는 코드는 다음과 같다.

```python
# simple_equation.py

from __future__ import annotations
from typing import Tuple, List
from chromosome import Chromosome
from genetic_algorithm import GeneticAlgorithm
from random import randrange, random
from copy import deepcopy

# Chromosome 클래스를 상속받으며, 말 그대로 간단한 방적식을 푼다.
class SimpleEquation(Chromosome):
    def __init__(self, x: int, y: int) -> None:
        self.x: int = x
        self.y: int = y

    # 방정식에서 x와 y를 계산
    # x와 y 값이 클수록 GeneticAlgorithm 클래스에 따라 염색체 개체의 적합도가 더 높아진다
    def fitness(self) -> float: # 6x - x^2 + 4y - y^2
        return 6 * self.x - self.x * self.x + 4 * self.y - self.y * self.y

    # 새 SimpleEquation 클래스를 이러한 값으로 인스턴스화
    @classmethod
    def random_instance(cls) -> SimpleEquation:
        return SimpleEquation(randrange(100), randrange(100))

    # 한 SimpleEquation 인스턴스를 다른 인스턴스와 결합하기 위해 단순히 두 인스턴스의 y값을 바꿔(swap) 두 자식을 만듦
    def crossover(self, other: SimpleEquation) -> Tuple[SimpleEquation, SimpleEquation]:
        child1: SimpleEquation = deepcopy(self)
        child2: SimpleEquation = deepcopy(other)
        child1.y = other.y
        child2.y = self.y
        return child1, child2

    # x 또는 y를 무작위로 증가 또는 감소
    def mutate(self) -> None:
        if random() > 0.5: # x를 돌연변이
            if random() > 0.5:
                self.x += 1
            else:
                self.x -= 1
        else: # 그렇지 않으면 y를 돌연변이
            if random() > 0.5:
                self.y += 1
            else:
                self.y -= 1

    def __str__(self) -> str:
        return f"X: {self.x} Y: {self.y} 적합도: {self.fitness()}"


if __name__ == "__main__":
    initial_population: List[SimpleEquation] = [SimpleEquation.random_instance() for _ in range(20)]
    ga: GeneticAlgorithm[SimpleEquation] = GeneticAlgorithm(initial_population=initial_population, threshold=13.0, max_generations = 100, mutation_chance = 0.1, crossover_chance = 0.7)
    result: SimpleEquation = ga.run()
    print(result)
```
```text
여기서 사용된 매개변수는 추측과 확인을 통해 얻은 것이다.
다른 매개변숫값을 사용할 수도 있다.
임곗값(threshold)은 13으로 설정한다. x=3이고 y=2일때 방적식의 값이 13이라는 것을 이미 알고 있기 때문이다.

만일 사전에 답을 알지 못했다면, 몇 세대에 걸쳐 최상의 결과를 찾아야 한다.
이 경우 임의로 많은 수의 임곗값을 설정하게 된다.
유전 알고리즘은 확률적이므로 모든 실행 결과는 다르다는 것을 명심해야 한다.
```

**유전 알고리즘**은 다른 해결 방법보다 더 많은 계산이 필요하다는 것을 고려해야 한다.

현실에서는 이러한 단순 최대화 문제에 유전 알고리즘을 적용할 수 없다.

여기에서는 유전 알고리즘이 효과가 있음을 살펴보기 위해 간단한 예를 살펴봤다.

___

### 5.4 SEND + MORE = MONEY 다시 보기

3장에서 **제약 만족 프레임워크**를 사용하여 고전 암호 해독 문제인 **SEND+MORE=MONEY**를 해결했다.

여기에서는 **유전 알고리즘**을 통해 합리적인 시간 내에 해결할 수 있다는 것을 보여줄 것이다.

유전 알고리즘 솔루션에 대한 문제를 공식화하는 데 있어서 가장 큰 어려움 중 하나는 공식을 어떻게 표현할지 결정하는 것이다.

암호 연산 문제를 나타내는 데 편리한 표현은 리스트 인덱스를 숫자로 사용하는 것이다.

따라서 가능한 **10자리(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)** 를 나타내기 위해 10개의 항목 리스트가 필요하다.

`SEND+MORE=MONEY`에는 **8개의 고유 문자(S, E, N, D, M, O, R, Y)** 가 있으며, 배열의 *마지막 두 항목은 공백*으로 남겨두고, 공백은 문자가 없음을 의미한다.

```python
# send_more_money2.py

from __future__ import annotations
from typing import Tuple, List
from chromosome import Chromosome
from genetic_algorithm import GeneticAlgorithm
from random import shuffle, sample
from copy import deepcopy


class SendMoreMoney2(Chromosome):
    def __init__(self, letters: List[str]) -> None:
        self.letters: List[str] = letters

    def fitness(self) -> float:
        s: int = self.letters.index("S")
        e: int = self.letters.index("E")
        n: int = self.letters.index("N")
        d: int = self.letters.index("D")
        m: int = self.letters.index("M")
        o: int = self.letters.index("O")
        r: int = self.letters.index("R")
        y: int = self.letters.index("Y")
        send: int = s * 1000 + e * 100 + n * 10 + d
        more: int = m * 1000 + o * 100 + r * 10 + e
        money: int = m * 10000 + o * 1000 + n * 100 + e * 10 + y
        difference: int = abs(money - (send + more))
        return 1 / (difference + 1)

    @classmethod
    def random_instance(cls) -> SendMoreMoney2:
        letters = ["S", "E", "N", "D", "M", "O", "R", "Y", " ", " "]
        shuffle(letters)
        return SendMoreMoney2(letters)

    def crossover(self, other: SendMoreMoney2) -> Tuple[SendMoreMoney2, SendMoreMoney2]:
        child1: SendMoreMoney2 = deepcopy(self)
        child2: SendMoreMoney2 = deepcopy(other)
        idx1, idx2 = sample(range(len(self.letters)), k=2)
        l1, l2 = child1.letters[idx1], child2.letters[idx2]
        child1.letters[child1.letters.index(l2)], child1.letters[idx2] = child1.letters[idx2], l2
        child2.letters[child2.letters.index(l1)], child2.letters[idx1] = child2.letters[idx1], l1
        return child1, child2

    def mutate(self) -> None: # 두 문자의 위치를 바꾼다
        idx1, idx2 = sample(range(len(self.letters)), k=2)
        self.letters[idx1], self.letters[idx2] = self.letters[idx2], self.letters[idx1]

    def __str__(self) -> str:
        s: int = self.letters.index("S")
        e: int = self.letters.index("E")
        n: int = self.letters.index("N")
        d: int = self.letters.index("D")
        m: int = self.letters.index("M")
        o: int = self.letters.index("O")
        r: int = self.letters.index("R")
        y: int = self.letters.index("Y")
        send: int = s * 1000 + e * 100 + n * 10 + d
        more: int = m * 1000 + o * 100 + r * 10 + e
        money: int = m * 10000 + o * 1000 + n * 100 + e * 10 + y
        difference: int = abs(money - (send + more))
        return f"{send} + {more} = {money} 차이: {difference}"


if __name__ == "__main__":
    initial_population: List[SendMoreMoney2] = [SendMoreMoney2.random_instance() for _ in range(1000)]
    ga: GeneticAlgorithm[SendMoreMoney2] = GeneticAlgorithm(initial_population=initial_population, threshold=1.0, max_generations = 1000, mutation_chance = 0.2, crossover_chance = 0.7, selection_type=GeneticAlgorithm.SelectionType.ROULETTE)
    result: SendMoreMoney2 = ga.run()
    print(result)
```

3장의 `satisfied() 메서드`와 이 장의 `fitness() 메서드`는 큰 차이가 있다.

여기서는 `1 / (difference + 1)`을 반환한다.

`difference`는 **`MONEY`와 `SEND+MORE` 차이의 절댓값**이다.

이것은 *염색체가 문제를 해결하는 데 얼마나 멀리 떨어져 있는지* 보여준다.

`fitness() 메서드`에서 값을 최소화하는 경우 `difference`를 자체적으로 반환하는 것이 좋다.

그러나 `GeneticAlgorithm`에서 `fitness() 메서드`의 가치를 극대화하기 위해서는 이를 뒤집을 필요가 있기 때문에, 1을 `difference`로 나눈다. 

1이 먼저 `difference`에 더해지므로 `difference`가 0이면 `fitness() 메서드`는 0이 아니라 1이 된다.


| difference | difference + 1 | fitness(1 / difference + 1) |
| ---------: | -------------: | --------------------------: |
|0           | 1              |                           1 |
|1           | 2              |                        0.5  |
|2           | 3              |                      0.25   |
|3           | 4              |                        0.125|



**차이가 작을수록** 좋고, **적합도가 높을수록** 좋다.

위 수식은 두 요소를 일렬로 표시하므로 잘 작동한다.

1을 적합도로 나누는 것은 최소화 문제를 최대화 문제로 변환하는 간단한 방법이지만, 이 방법에는 편향이 있어서 절대로 안전한 방법은 아니다.

____


### 5.5 최적화 리스트 압축

압축하려는 정보가 있다고 가정하여 리스트로 구성되어 있으며, 모든 항목이 손상되지 않는 한 항목 순서는 신경 쓰지 않는다.

- 어떤 항목의 순서가 압축 비율을 최대화할까?

- 항목의 순서가 대부분의 압축 알고리즘의 압축 비율에 영향이 있다는 것을 알고 있는가?

이에 대한 답은 **압축 알고리즘에 따라 압축 비율이 다르다는 것**이다.

```python
# list_compression.py

from __future__ import annotations
from typing import Tuple, List, Any
from chromosome import Chromosome
from genetic_algorithm import GeneticAlgorithm
from random import shuffle, sample
from copy import deepcopy
from zlib import compress
from sys import getsizeof
from pickle import dumps

# 165바이트 압축됨
PEOPLE: List[str] = ["Michael", "Sarah", "Joshua", "Narine", "David", "Sajid", "Melanie", "Daniel", "Wei", "Dean", "Brian", "Murat", "Lisa"]


class ListCompression(Chromosome):
    def __init__(self, lst: List[Any]) -> None:
        self.lst: List[Any] = lst

    @property
    def bytes_compressed(self) -> int:
        return getsizeof(compress(dumps(self.lst)))

    def fitness(self) -> float:
        return 1 / self.bytes_compressed

    @classmethod
    def random_instance(cls) -> ListCompression:
        mylst: List[str] = deepcopy(PEOPLE)
        shuffle(mylst)
        return ListCompression(mylst)

    def crossover(self, other: ListCompression) -> Tuple[ListCompression, ListCompression]:
        child1: ListCompression = deepcopy(self)
        child2: ListCompression = deepcopy(other)
        idx1, idx2 = sample(range(len(self.lst)), k=2)
        l1, l2 = child1.lst[idx1], child2.lst[idx2]
        child1.lst[child1.lst.index(l2)], child1.lst[idx2] = child1.lst[idx2], l2
        child2.lst[child2.lst.index(l1)], child2.lst[idx1] = child2.lst[idx1], l1
        return child1, child2

    def mutate(self) -> None: # 두 위치를 스왑
        idx1, idx2 = sample(range(len(self.lst)), k=2)
        self.lst[idx1], self.lst[idx2] = self.lst[idx2], self.lst[idx1]

    def __str__(self) -> str:
        return f"순서: {self.lst} 바이트: {self.bytes_compressed}"


if __name__ == "__main__":
    initial_population: List[ListCompression] = [ListCompression.random_instance() for _ in range(100)]
    ga: GeneticAlgorithm[ListCompression] = GeneticAlgorithm(initial_population=initial_population, threshold=1.0, max_generations = 100, mutation_chance = 0.2, crossover_chance = 0.7, selection_type=GeneticAlgorithm.SelectionType.TOURNAMENT)
    result: ListCompression = ga.run()
    print(result)
```

위의 `list_compression 코드`와 `SEND+MORE=MONEY 다시 보기의 코드` 모두 **리스트 항목을 가져온 뒤 계속해서 재배열하고 이를 테스트**하고 있다.

다양한 문제를 해결하는 **제네릭 슈퍼클래스를 작성**하여 두 문제 해결에 사용할 수 있다.

리스트에서 최적의 순서를 찾아야 하는 모든 문제는 같은 방식으로 해결할 수 있다.

유일하게 다른 부분은 *서브클래스에서 각자의 적합도 메서드를 구현*한 것이다.


___

### 5.6 유전 알고리즘에 대한 도전

유전 알고리즘은 실제로 문제의 대부분에 적합하지 않다.

빠른 결정론적 알고리즘이 존재하는 문제에 대해 유전 알고리즘은 의미가 없다.

확률적 특성으로 인해 실행시간을 예측할 수 없다.

이 문제를 해결하기 위해 특정 세대까지만 실행할 수 있으나, 최적의 솔루션을 찾았는지 확실하게 알 수 없다.

즉, **유전 알고리즘은 더 나은 솔루션이 존재하지 않는다고 확신할 때만 선택해야 한다는 사실**을 나타낸다.

유전 알고리즘의 또 다른 문제는 염색체로 문제에 대한 잠재적 솔루션을 나타내는 방법을 결정하는 것이다.

전통적인 관행은 대부분의 문제를 이진 문자열(0과 1의 시퀀스, 원시 비트)로 표현하는 것이다.

이것은 공간 측면에서 최적이며, 크로스오버 메서드를 쉽게 구현할 수 있다.

그러나 대부분의 복잡한 문제는 나눌 수 있는 비트 문자열로 쉽게 표현되지 않는다.

또 다른 문제는 룰렛휠 선택 방법이다.

적합도 비례 선택이라고 부르는 룰렛휠 선택은 선택이 실행될 때마다 상대적으로 적합한 개체의 우위로 인해 집단의 다양성 부족을 초래할 수 있다.

반면 적합도값이 서로 비슷하다면 룰렛휠 선택은 선택 압력이 부족할 수 있다.

요약하자면, 유전 알고리즘을 사용할 만큼 충분히 큰 문제에 대해 제네릭 알고리즘은 예측 가능한 시간 내에 최적의 솔루션을 찾을 수 없다. 

이러한 이유로 유전 알고리즘은 최적의 솔루션을 요구하지 않고 충분히 좋은 솔루션을 요구하는 상황 에서 가장 잘 활용할 수 있다.

___

### 5.7 적용사례

유전 알고리즘은 많은 문제에 효과적으로 사용되는데, 제약 충족 문제와 같은 기존 방법으로 해결할 수 없는 아주 큰 문제와 같이 완벽한 최적의 솔루션이 필요 없는 어려운 문제에 자주 사용된다.

  ```
  유전 알고리즘은 전산 생물학에서 많이 활용되었다.
  수용체에 결합될 때 소분자의 구성을 찾는 단백질-리간드 결합에 성공적으로 사용했다.
  이는 제약 연구 및 자연 메커니즘을 더 잘 이해하는 데 사용된다.
  ```
  
  ```
  9장에서 살펴볼 외판원 문제는 컴퓨터 과학에서 가장 유명한 문제 중 하나다.
  판매원이 모든 도시를 정확히 한 번 방문하고, 출발지로 다시 오는 가장 짧은 경로를 찾는 것이다.
  외판원 문제에서 솔루션은 모든 도시를 방문하는 비용을 최소화하는 사이클이지만, 최소 신장 트리는 모든 도시를 연결하는 비용을 최소화하는 트리다.
   ```
   
  ```
  컴퓨터 제네레이티드 아트에서 유전 알고리즘은 확률을 사용하여 사진을 모방하는 데 사용한다.
  화면에 무작위로 배치된 50개의 다각형이 한 사진에 최대한 가깝게 일치할 때까지 점차적으로 비틀어지고, 회전하고, 이동하고, 크기가 조정되고, 색상이 변하여 결과를 보인다.
  ```
  
  ```
  유전 알고리즘은 진화 연산이라고 하는 더 큰 분야의 일부다.
  유전 알고리즘과 밀접한 관련이 있는 진화 연산의 한 영역은 유전 프로그맹인데, 여기에서 프로그램은 선택, 크로스오버 및 돌연변이 연산을 사용하여, 프로그래밍 문제에 대한 명백한 해결책을 찾기 위해 스스로 설정을 수정한다.
  ```
  
유전 알고리즘의 장점은 쉽게 병렬화할 수 있다는 것이다.

가장 명백한 형태로 각 모집단을 별도의 프로세서에서 시뮬레이션할 수 있다.

가장 세분화된 형태로 각 개체는 변이 및 교차될 수 있고, 분리된 스레드에서 개체의 적합도를 계산할 수 있고, 그 사이에는 많은 가능성이 존재한다.

___

### 5.8 연습문제

#### (1)
**체감 확률을 기반으로 로는 두 번째 혹은 세 번째로 가장 좋은 염색체를 선택할 수 있는 고급 토너먼트 선택 유형을 GeneticAlgorithm 클래스에 추가하라.**

#### (2)
**3장의 제약 충족 문제 프레임워크에 유전 알고리즘을 사용하여 임의의 제약 충족 문제를 해결하는 새로운 메서드를 추가하라. 적합도의 가능한 측정은 염색체에 의해 해결되는 제약 조건의 수다.**

#### (3)
**Chromosome 클래스를 상속받는 BitString 클래스를 생성하라. 비트 문자열에 대해서는 1장을 참조한다. 그리고 새로 생성한 클래스를 사용하여 5.3절의 '간단한 방정식'문제를 해결하라. 문제를 어떻게 비트 문자열로 인코딩할 수 있을까?**


___

## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
