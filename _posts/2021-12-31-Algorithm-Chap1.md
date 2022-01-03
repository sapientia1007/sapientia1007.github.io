---
layout: post
title: Algorithm - Chap 1
subtitle: Chap1
categories: Algorithm
tags: Algorithm
---

## Chap1 - 작은 문제


### 1 . 1 - 피보나치 수열
피보나치 수열은 첫 번째와 두 번째 숫자를 제외한 모든 숫자가 이전 두 숫자를 합한 숫자를 나열한 수열이다. 이때 첫 번째와 두 번째 숫자는 각각 0과 1이다.
```markdown
fib(n) = fib(n-1) + fib(n-2)
```
피보나치 수의 값을 계산하기 위한 위 수식은 **재귀 함수(자기 자신을 호출하는 함수)로 쉽게 구현할 수 있는 의사코드**이다.


```python
def fib1(n: int) -> int :
  return fib1(n-1) + fib1(n-2)
```
위 코드를 실행하면 에러가 발생하는데, 최종 결과를 반환하지 않고 계속 실행하는 무한 루프와 유사한 **무한 재귀**가 일어난다.
이때, **무한 재귀**가 일어나는 이유는 재귀 함수에서 탈출 조건인 **기저 조건**을 설정하지 않았기 때문이다.


피보나치 수열의 경우 `특수한 초기값인 0과 1의 두 수열값`으로 **기저 조건**을 설정한다.
```python
def fib2(n: int) -> int :
  if n < 2: # 기저 조건
    return n
  return fib1(n-1) + fib1(n-2) # 재귀
```

위 `fib2()`를 호출할때마다 `fib2(n-1)`과 `fib2(n-2)`을 통해 `fib2()`가 두 번 더 호출되는 것으로 보아, 인자가 커질수록 호출 트리가 기하급수적으로 커진다. 
**즉, 수열의 요소 숫자가 증가할수록 함수 호출 증가 횟수는 더 악화된다.**


이처럼 **기하급수적으로 커지는 함수 호출 횟수를 줄이기** 위해, 이전에 실행된것과 같은 계산을 수행할 때 다시 계산하지 않고 `저장된 값을 사용`할 수 있는 기술인 **메모이제이션**을 사용합니다. 

즉, **메모이제이션**은 계산 작업이 완료되면 결과를 저장하는 기술이다. 

```python
from typing import Dict
memo : Dict[int, int] = {0 : 0, 1: 1} # 기저 조건

def fib3(n : int) -> int :
  if n not in memo :
    memo[n] = fib3(n-1) + fib3(n-2) # 메모이제이션
  return memo[n]
```
**메모이제이션**은 초기 기저 조건인 0과 1을 미리 저장한 후, 그 후 **계산되는 값을 계속 저장**하면서, **`memo`에 미리 계산되서 저장된 요소가 있으면 다시 계산하지 않고 결과를 반환**한다.


파이썬에서는 모든 함수를 자동으로 메모이징하는 내장형 데커레이터가 있는데, **@functools.lru_cache() 데커레이터**를 사용하여 **계산된 반환값을 메모리에 저장**한다. 이후 다시 동일한 인자가 실행되면 저장된 값을 검색하여 반환한다.
(이때, **@lru_cache**의 maxsize 속성은 데커레이터 함수에서 가장 최근의 호출을 캐시할 수 있는 크기이고, **None은 캐시에 제한이 없다는 것을 의미**한다.)

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fib4(n : int) -> int :
  if n < 2 : # 기저 조건
    return n
  return fib4(n-2) + fib4(n-1) # 재귀
```

지금까지 작성했던 피보나치 수열을 고전 방식으로 작성하면 다음과 같다.
```python
def fib5(n : int) -> int :
  if n == 0 : return n # 특수 조건
  last : int = 0
  next : int = 1
  for _ in range(1, n) : 
    last, next = next, last + next
  return next
```
_이때, `fib5()`의 for문은 **튜플 언패킹(Tuple Unpacking)**을 사용하여, **last는 '변수 next의 이전 값'으로 설정되고, 변수 next는 '변수 last 이전 값 + 변수 next의 이전 값'으로 설정**된다.
즉, **변수 last가 갱신된 후, 변수 next가 갱신되기 전에 변수 next의 이전 값을 저장할 임시 변수를 만들지 않는다.**_

위의 `fib5()` 방법을 사용하면, for문이 **최대 n-1번 실행**된다. 이것은 피보나치 수열을 구하는 가장 효율적인 방법이다.

그 이유로는 **단순 재귀**를 사용했을 때는 **상향식(bottom-up)** 으로 계산하지만, 위와 같은 **반복문**을 사용했을 때는 **하향식(top-down)** 으로 계산하기 때문이다.

때때로 재귀가 문제를 해결하는 가장 직관적인 방법이지만, 단순 재귀는 성능에 상당한 문제를 일으킬 수 있다. 또한, 재귀적으로 해결할 수 있는 문제는 반복문으로도 해결할 수 있다.


_해당 단일값까지 전체 수열을 구하려면_ **yield**문을 사용하여 **파이썬 제너레이터**로 쉽게 변환하고, 제너레이터를 순환할 때 각 반복은 **yield 문을 사용해 피보나치 수를 반환**한다.
```python
from typing import Generator

def fib6(n : int) -> Generator[int, None, None] :
  yield 0 # 특수 조건
  if n > 0 : yield 1 # 특수 조건
  last : int = 0 # fib(0)
  next : int = 1 # fib(1)
  for _ in range(1, n) :
    last, next = next, last + next
    yield next # 제너레이터 핵심 반환문


for i in fib6(50) : 
  print(i)
```

이때 매 반복마다 `yield문`이 실행되고, 끝에 도달하여 더 이상 반환될 `yield문이 없다`면 **for문은 반복을 종료**한다.

***

### 1 . 2 - 압축 알고리즘 

**압축**은 공간을 덜 차지하는 방식으로 **데이터를 인코딩하는 행위**이다. **압축 풀기**는 압축의 역과정으로 **원래 형식으로 데이터를 되돌리는 디코딩하는 행위**이다.

**데이터를 압축**하면 *저장 공간의 효율성이 높아*지지만 모든 데이터를 압축하지 않는 이유는 시간과 공간 사이에 **트레이드오프(tradeoff, 절충점)** 이 있기 때문이다. 
이는 *데이터를 압축하고 원래 형식으로 되돌리려면 시간이 걸린다는 것을 의미한다.* 즉, **데이터 압축**은 데이터가 빠르게 실행되는 것보다 **작은 저장 공간을 차지**하는 것이 우선순위가 높은 상황에서만 의미가 있다.

일반적으로, **데이터 압축**은 데이터 저장 타입이 해당 데이터에 대해 엄격하게 필요한 것보다 더 많은 비트를 사용한다고 깨달았을 때 더 쉽게 이루어진다. 
예를 들면, 로우레벨을 생각해보면 65.635를 넘지 않은 부호 없는 정수가 64비트 부호 없는 정수로 메모리에 저장되는 경우 데이터가 비효율적으로 저장된다. 이때 64비트 대신 16비트 부호 없는 정수로 저장하여 정수의 공간 사용량을 75% 줄일 수 있다. 

*수백만 개의 이러한 숫자가 비효율적으로 저장되는 경우 최대 메가바이트의 공간이 낭비될 수 있다.*

*어떤 한 타입으로 표현된 다른 값의 수가 이 값을 저장하는 데 사용되는 비트로 표현할 수 있는 수보다 적다면 이 값을 더 효율적으로 저장할 수 있다.*

파이썬 표준 라이브러리에서는 임의 길이의 비트 문자열을 다루기 위한 구조체를 제공하지 않는다. 다음 코드는 A, C, G, T로 구성된 문자열을 비트 문자열로 변환하여 반환한다. 비트 문자열은 정수로 저장되며, 정수는 어떤 길이의 비트 문자열로도 사용될 수 있다. 정수를 다시 문자열로 변환하려면 특수 메서드 `__str__()` 를 구현한다.
```python
class CompressedGene:
    def __init__(self, gene: str) -> None:
        self._compress(gene)

    def _compress(self, gene: str) -> None:
        self.bit_string: int = 1  # start with sentinel
        for nucleotide in gene.upper():
            self.bit_string <<= 2  # shift left two bits
            if nucleotide == "A":  # change last two bits to 00
                self.bit_string |= 0b00
            elif nucleotide == "C":  # change last two bits to 01
                self.bit_string |= 0b01
            elif nucleotide == "G":  # change last two bits to 10
                self.bit_string |= 0b10
            elif nucleotide == "T":  # change last two bits to 11
                self.bit_string |= 0b11
            else:
                raise ValueError("Invalid Nucleotide:{}".format(nucleotide))

    def decompress(self) -> str:
        gene: str = ""
        for i in range(0, self.bit_string.bit_length() - 1, 2):  # - 1 to exclude sentinel
            bits: int = self.bit_string >> i & 0b11  # get just 2 relevant bits
            if bits == 0b00:  # A
                gene += "A"
            elif bits == 0b01:  # C
                gene += "C"
            elif bits == 0b10:  # G
                gene += "G"
            elif bits == 0b11:  # T
                gene += "T"
            else:
                raise ValueError("Invalid bits:{}".format(bits))
        return gene[::-1]  # [::-1] reverses string by slicing backwards

    def __str__(self) -> str:  # string representation for pretty printing
        return self.decompress()



from sys import getsizeof
original: str = "TAGGGATTAACCGTTATATATATATAGCCATGGATCGATTATATAGGGATTAACCGTTATATATATATAGCCATGGATCGATTATA" * 100
print("original is {} bytes".format(getsizeof(original)))
compressed: CompressedGene = CompressedGene(original)  # compress
print("compressed is {} bytes".format(getsizeof(compressed.bit_string)))
print(compressed)  # decompress
print("original and decompressed are the same: {}".format(original == compressed.decompress()))
```

```python
class CompressedGene :
  def __init__(self, gene : str) -> None :
    self._compress(gene)
```
`CompressedGene 클래스`의 `__init__()` 메서드는 **유전자 뉴클레오타이드의 문자열 시퀀스를 인자로 받아서 데이터를 초기화**한다. `_compress()` 메서드는 **뉴클레오타이드 문자열 시퀀스를 비트 문자열로 반환**한다.

위의 코드에서, `_compress()` 메서드는 **언더스코어**로 시작한다. 파이썬은 변수와 메서드에 대한 접근 제한자가 없는 대신 모든 변수와 메서드는 리플렉션을 통해 접근할 수 있다. 즉, **메서드 이름 앞의 언더스코어는 이 메서드가 클래스 외부에서 사용되지 않게 하기 위한 컨벤션**으로, 클래스 내부 객체가 변경될 수 있으므로 비공개로 처리한다는 것을 의미한다. 또한, *클래스에서 두 개의 언더스코어로 시작하는 메서드나 인스턴스 변수*가 있다면 파이썬은 이들을 **네임 맹글링(name mangle)** 이라 하고, `이들의 이름을 특별한 값으로 변경`하여 다른 클래스에서 쉽게 접근 할수 없게 한다.



```python
def _compress(self, gene : str) -> None : 
  self.bit_string : int = 1 # 1로 시작
  for nucleotide in gene.upper() :
    self.bit_string <<= 2 # 왼쪽으로 2비트 시프트
    if nucleotide == "A" : # 마지막 2 비트를 00으로 변경
      self.bit_string |= 0b00
    elif nucleotide == "C" : # 마지막 2 비트를 01로 변경
      self.bit_string |= 0b01
    elif nucleotide == "G" : # 마지막 2 비트를 10으로 변경
      self.bit_string |= 0b10
    elif nucleotide == "C" : # 마지막 2 비트를 11로 변경
      self.bit_string |= 0b11
    else :
      raise ValeError("유효하지 않은 뉴클레오타이드입니다:{}".format(nucleotide))
```

위 코드는 실제 압축을 수행한 코드로, `_compress()` 메서드는 뉴클레오타이드 문자열의 각 문자를 순차적으로 살펴본다. 
문자가 **A**면 비트 문자열에 **00**을 추가하고, 문자가 **C**면 비트 문자열에 **01**을 추가하는 식이다. 

각 뉴클레오타이드마다 **2비트**가 필요하므로 각각의 **새로운 뉴클레오타이드를 추가하기 전**에 `비트 문자열을 2비트 왼쪽으로 시프트`한다.
**2비트 왼쪽으로 시프트**하면 비트 문자열 오른쪽에 **두 개의 0**이 추가되고, **or연산자(|)** 를 사용하여 **다른 비트를 추가**하는데, 이는 `두 개의 0이 아닌 다른 비트의 값으로 대체`되는 동작과 동일하다. 
즉, **비트 문자열의 오른쪽에 두 개의 새로운 비트를 계속 추가**하는데, 뉴클레오타이드 타입에 따라 두 비트가 결정된다.


```python
def decompress(self) -> str :
  gene : str = ""
  for i in range(0, self.bit_string.bit_length() - 1, 2) : # 1로 시작하므로 1을 뺀다.
    bits : int = self.bit_string >> i && 0b11
    if bits == 0b00 : # A
      gene += "A"
    elif bits == 0b01 : # C
      gene += "C"
    elif bits == 0b10 : # G
      gene += "G"
    elif bits == 0b11 : # T
      gene += "T"
    else :
      raise ValueError("Invalid bits:{}".format(bits))
  return gene[::-1]


def __str__(self) -> str :
  return self.decompress()

```
위는 압축을 해제하는 `decompress()` 메서드와 이를 사용하는 `__str__()` 특수 메서드를 구현한것으로, `decompress()` 메서드는 비트 문자열에서 *한 번에 2 비트를 읽어*서 **변수 gene끝에 문자를 추가**한다. 왼쪽에서 오른쪽으로 압축되는 대신, 압축된 순서를 오른쪽에서 왼쪽으로 봤을때, 비트가 역방향으로 읽혀지기 때문에 슬라이싱 `[::-1]`을 사용하여 마지막에 문자열을 뒤집는다.

***

### 1 . 3 - 깨지지 않는 암호화
**일회용 암호(OTP)** 는 *원본 데이터*와 *의미 없는 무작위 더미 데이터*를 결합하여 **원본 데이터를 암호화**한다. 이때 두 개의 키를 가진 암호화기가 생성되는데 하나는 **프로덕트 키**고 다른 하나는 **무작위 더미 데이터 키**다. 이 *두 키의 접근 없이는 원본 데이터를 다시 구성할 수 없다*. 또한, *둘 중의 하나의 키만 있으면 암호를 풀지 못한다.* 즉, 두 키의 쌍을 통해 복호화가 이루어진다. 또한, 어떤 데이터의 암호화를 잘 수행했다면 일회용 암호는 깨지지 않는 암호화 형식이다.

**일회용 암호**에서 암호화 작업에 사용된 **더미 데이터**는 *암호화 결과의 프로덕트 키가 깨지지 않도록 하기 위한 세 가지 기준*이 있다.
* 첫째, 더미 데이터는 원본 데이터의 길이와 같아야 한다. 
* 둘째, 더미 데이터는 무작위여야 한다. 
* 셋째, 더미 데이터는 비밀이어야 한다. 

첫 번째와 세 번째는 기본 조건인데, 이는 더미 데이터가 너무 짧으면 반복을 통해 쉽게 패턴이 노출될 수 있기 때문이고, 두 키 중 하나가 공개된다면 단서를 얻을 수 있기 때문이다. 

이때, 두 번째 기준에서 우리는 '**진정한 무작위 데이터를 생성**할 수 있을까'에 대한 의문을 가지게 되는데, 이의 답은 **대부분의 컴퓨터는 생성하지 못한다**는 것이다.


```python
from secrets import token_bytes
from typing import Tuple

def random_key(length : int) -> int :
  tb : byyes = token_bytes(length) # length만큼 임의의 바이트 생성
  return int.from_bytes(tb, "big") # 바이트를 비트 문자열로 변환한 후 반환
```
위 예제는 `secret 모듈`에서 `token_bytes()` 함수를 사용하여 **의사난수 데이터를 생성**하는 것이다. 이 데이터는 *진정한 난수가 아니*지만 `secret 모듈`의 `token_bytes()` 함수는 예제 사용 목적을 충분히 만족하고, 이를 사용해서 *더미 데이터로 사용할 임의의 키를 생성*한다.
이 함수는 **길이만큼 임의의 바이트로 채워진 정수를 생성**한다.
이때, 어떻게 **여러 바이트**를 **단일 정수**로 변환할 수 있는 방법은 `'압축 알고리즘'`에 있다. 이전에 임의 크기의 정수를 일반 비트 문자열로 사용했는데, 여기서도 정수를 그와 같은 방식으로 사용한다. 이는 **비트 단위 연산은 시퀀스의 많은 개별 바이트보다 단일 정수를 통해 쉽고 효율적으로 계산**할 수 있으므로 유용하다. 여기서는 비트 연산을 위해 **XOR**을 사용한다.


**원본 데이터**와 **더미 데이터**는 `XOR 연산으로 조합을 수행`한다.

`XOR(eXclusice OR, 배타적 논리합)`은 피연산자 중 하나가 참일때 true를 반환하지만, 둘 다 참이거나 거짓이면 false를 반환하는 논리 비트 연산이다. 파이썬에서 **XOR 연산자**는 `^`이다.
**두 숫자의 비트가 XOR연산자를 사용하여 결합**되는 경우 유용한 속성은 **연산 결과를 피연산자 중 하나와 재결합시켜 다른 피연산자를 생성**할 수 있다는 것이다.

`XOR 연산`은 **일회용 암호의 기초를 형성**한다. *프로덕트 키를 생성*하기 위해 *'원본 문자열에서 바이트를 나타내는 정수'와 'random_key() 함수에 의해 생성된 같은 비트 길이의 무작위 정수'를 XOR연산*한다.



```python
def encrypt(original : str) -> Tuple[int, int] : 
  original_bytes : bytes = original.encode()
  dummy : int = random_key(len(original_bytes))
  original_key : int = int_from.bytes(original_bytes, "big")
  encrypted : int = original_key ^ dummy 
  return dummy, encrypted

def decrypt(key1 : int, key2 : int) -> str:
  decrypted : int = key1 ^ key2
  temp : bytes = decrypted.to_bytes((decrpyted.bit_length() + 7) // 8, "big")
  return temp.decode()
```
위 `encrypt()` 함수는 한 쌍의 더미 키와 프로덕트 키를 반환한다. 
복호화는 단순히 `encrypt()`함수에서 생성한 키 쌍을 **재결합**하는 문제다. 즉, `encrypt()` 함수에서 **반환된 두 키의 각 비트**를 다시 `XOR 연산`하여 **복호화**를 수행하고, 최종 결과는 문자열이다. 먼저 정수 `int.to_bytes()` 메서드를 사용하여 바이트로 변환된다. 이 메서드는 정수가 변환할 바이트 수를 인자로 취한다. 이 수를 얻기 위해 길이를 8로 나눈다. 마지막으로 바이트 `decode()` 메서드에서 문자열을 반환한다. off-by-one 오류(잘못된 숫자로 비교할 때나, 시퀀스가 1이 아닌 0에서 시작한다는 점을 고려하지 않을 때 발생)를 피하기 위해 정수 나누기 연산자를 사용하여 8로 나누기 전에 복호화된 데이터의 길이에 7을 더하여 반올림해야한다. 

**일회용 암호의 암호화가 잘 작동하면 동일한 유니코드 문자열을 문제없이 암호화하고 복호화할 수 있어야 한다.**

```python
if __name__ == "__main__" :
  key1, key2 = encrypt("One Time Pad!")
  result : str = decrypt(key1, key2)
  print(result)
```
화면에 "One Time Pad!"가 출력되면 암호화 및 복호화가 성공했다는 것을 의미한다.


***

### 1 . 4 - 파이 계산하기
수학적으로 중요한 수인 **파이π**는 많은 공식에 사용된다. 가장 간단한 예는 `라이프니츠 공식`이다. 

다음 무한급수의 합은 파이와 같다.
``π = 4/1 - 4/3 + 4/5 - 4/7 + 4/9 - 4/11 ...``
무한급수의 분자는 4로 유지하고, 분모는 2씩 증가하며, 빼기와 더하기 연산이 번갈아 나타난다.

공식의 일부를 함수의 변수로 변환하여 급수를 직접 모델링할 수 있다. 분자는 상수 4이며, 분모는 1에서 시작하여 2씩 증가하는 변수가 된다. 연산은 더하기와 빼기 연산에 따라 -1 또는 1을 사용한다. 마지막으로 변수 pi는 다음 코드에서 for 문이 진행할 때 급수의 한계를 누적하는 데 사용된다.
```python
def calculate_pi(n_terms : int) -> float :
  numerator: float = 4.0
  denominator: float = 1.0
  operation: float = 1.0
  pi : float = 0.0
  for _ in range(n_terms) : 
    pi += operation * (numerator / denominator)
    denominator += 2.0
    operation *= -1.0
   return pi
```
이 함수는 수식을 코드로 **기계 변환**하여 *어떤 흥미로운 개념을 모델링하거나 시뮬레이션하는 간단하고 효과적인 예*이지만, 기계 변환은 가장 효율적인 솔루션이라고 할 수는 없다.


***


### 1 . 5 - 하노이 탑
![hanoi](http://jjhcom.github.io/assets/images/banners/hanoi.jpg) : <https://blog.naver.com/mathplant00/220489897756>

**하노이탑 문제**는 다음과 같은 제약 조건에서 탑 A에 끼워져 있는 3개의 디스크를 탑 C로 이동하는 것이다
* 한 번에 하나의 디스크만 이동할 수 있다.
* 탑에서 제일 위에 있는 디스크만 이동할 수 있다.
* 큰 디스크는 작은 디스크 위에 올 수없다.

**스택**은 **후입선출(Last-In-First-Out)** 의 개념으로 모델링한 자료구조로, 마지막으로 넣은 데이터가 가장 먼저 나온다. 

스택의 두 기본 연산은 **새 항목을 스택에 넣는 `푸시(push)`** 와 **스택에 마지막으로 넣은 항목을 제거하고 반환하는 `팝(pop)`** 이다.


*스택은 하노이탑 문제에서 탑이라고 보면 되는데, 탑에 디스크를 끼우려면 탑에 디스크를 넣으면 되고, 첫 번째 탑에서 두 번째 탑으로 디스크를 옮기려면 첫 번째 탑에서 디스크를 꺼내고 두 번째 탑에 넣으면 된다.*
```python
from typing import TypeVar, Generic, List
T = TypeVar('T')

class Stack(Generic[T]) :
  def __init__(self) -> None :
    self._container : List[T] = []
  
  def push(self, item : T) -> None :
    self._container.append(item)
  
  def pop(self) -> T :
    return self._container.pop()
  
  def __repr__(self) -> str : 
    return repr(self._container)
    
num_discs : int = 3
tower_a = Stack[int] = Stack()
tower_b = Stack[int] = Stack()
tower_c = Stack[int] = Stack()
for i in range(1, num_discs + 1) :
  tower_a.push(i)

def hanoi(begin : Stack[int], end : Stack[int], temp : Stack[int], n : int) -> None : 
  if n == 1 :
    end.push(begin.pop())
  else :
    hanoi(begin, temp, end, n-1)
    hanoi(begin, end, temp, 1)
    hanoi(temp, end, begin, n-1)
    

# 하노이탑 실행
hanoi(tower_a, tower_c, tower_b, num_discs)
print(tower_a)
print(tower_b)
print(tower_c)
```
하노이 탑의 **디스크 하나를 움직이는 것**은 하노이탑 문제에 대한 **재귀 함수의 기저 조건**이다. 
**재귀 조건**은 **둘 이상의 디스크를 움직**인다. 

따라서 **재귀 함수의 핵심은** 기본적으로 `하나의 디스크 이동(기저 조건)`과 `둘 이상의 디스크 이동(재귀 조건)`의 두 가지 시나리오가 필요하다.

하노이탑 문제에는 **하나의 디스크를 이동하는 간단한 기저 조건**과 **다른 두 개의 디스크를 모두 이동하는 재귀 조건**이 있다. 

재귀 조건은 다음과 같다.
* 상단의 n-1개의 디스크(가장 작은 디스크와 중간 디스크)를 탑 C를 이용하여 탑 A에서 탑 B로 임시 이동한다.
* 가장 큰 디스크를 A에서 C로 이동한다.
* 상단의 n-1개의 디스크를 탑 A를 이용하여 탑 B에서 탑 C로 이동한다.

이 재귀 알고리즘은 여러 디스크에도 적용할 수 있다. 

*여러 디스크를 이동하는 일반적인 재귀 알고리즘을 이해하고 작성하면 나머지 일은 컴퓨터가 알아서 처리하는데, 이것이 **재귀적 솔루션을 공식화**하는 힘이다. 즉, 디스크 하나하나를 어디로 옮길 것인지 **구체적인 방식**을 사용하지 않고 **추상적인 방식**으로 해결할 수 있다는 것을 의미한다.*


***

### 1 . 6 - 적용사례

이 장에서 소개한 **재귀, 메모이제이션, 압축, 비트 조작**과 같은 기술들이 없어도 `어떠한 문제를 해결`할 수는 있지만, 이 **기술들을 적용**하여 `더 논리적이 효울적으로 문제를 해결`할 수 있다. 

이 장에서 소개한 기술들을 간략하게 정리하면 다음과 같다.

* 재귀
  : *스킴(Scheme)과 하스켈(Haskell)* 과 같은 일부 **함수형 프로그래밍 언어**에서는 명령형 언어에서 사용하는 **반복문** 대신 `재귀`를 사용하지만, `재귀로 해결할 수 있는 문제는 반목문으로도 해결할 수 있다.`

* 메모이제이션
  : **파서(언어를 해석하는 프로그램, Parser)의 작업 속도를 높이기** 위해 사용하는데, `최근 계산 결과를 다시 사용할 가능성이 있는 모든 문제`에 유용하다. 즉, 동일한 함수가 한 번 더 호출될 때 이전에 함수 호출 결과를 저장했었으므로 함수를 다시 실행할 필요가 없다는 것이다.
  
* 압축
  : `압축 알고리즘`에서 살펴본 비트 문자열 기법은 **값의 수가 제한된 간단한 데이터 타입에 사용할 수 있다.** 즉, 대부분의 압축 알고리즘은 **반복되는 정보를 제거**할 수 있는 `데이터 집합 내의 패턴 또는 구조를 찾아서 동작`한다.
  
* 일회용 암호 
  : 일회용 암호는 일반적인 암호화에서는 실용적이지 않은데, **원본 데이터를 재구성**하기 위해 `암호화기`와 `복호화기`가 키들 중 하나를 가지고 있어야 하며, 이는 *키를 비밀로 유지*하고자 하는 **암호화 체계의 목표**를 달성하는 데 번거로운 일이다. 

___
## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
