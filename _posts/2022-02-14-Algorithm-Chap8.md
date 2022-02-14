---
layout: post
title: Algorithm - Chap 8
subtitle: Chap8
categories: Classic_Computer_Science
tags: Algorithm
use_math: true
---

## Chap8 - 적대적 탐색

2인용 게임, 제로섬, 완전 정보 게임은 **상대방의 게임 상태에 대한 모든 정보**를 가지고 있는 게임이며, 한 사람에게 이점이 있다면, 다른 사람에게는 불리한 점이 있다. 

이러한 게임에는 *틱택토, 커넥트포, 체커스, 체스* 등이 있다.

이 장에서는 이러한 게임을 할 수 있는 **인공적인 상대 플레이어**를 만드는 법을 배운다.

실제로 여기서 다루는 기술은 *현대 컴퓨팅 기술과 결합하여 간단한 게임을 완벽하게 실행하는 **인공적인 상대 플레이어***를 만들 수 있으며, 인간의 능력을 뛰어넘는 복잡한 게임을 할 수 있다. 


### 8.1 보드게임 구성 요소

이 책에서 다루는 대부분의 문제와 마찬가지로 가능한 한 **제네릭**을 사용하여 문제를 해결할 것이다.

**적대적 탐색**이 경우 탐색 알고리즘을 게임별로 지정하지 않는다.

**탐색 알고리즘**에 필요한 모든 상태를 정의하는 간단한 기본 클래스를 정의하여 구현을 시작한다.

이후에 구현하고자 하는 특정 게임(틱택토, 커넥트포)에서 **기본 클래스를 서브 클래스로 상속받고**, **서브 클래스에서 탐색 알고리즘을 사용하여** 게임을 실행한다.

```python
# board.py (기본 클래스)

from __future__ import annotations
from typing import NewType, List
from abc import ABC, abstractmethod

Move = NewType('Move', int)

class Piece:
    @property
    def opposite(self) -> Piece:
        raise NotImplementedError("서브클래스로 구현해야 합니다.")

class Board(ABC):
    @property
    @abstractmethod
    def turn(self) -> Piece:
        ...

    @abstractmethod
    def move(self, location: Move) -> Board:
        ...

    @property
    @abstractmethod
    def legal_moves(self) -> List[Move]:
        ...

    @property
    @abstractmethod
    def is_win(self) -> bool:
        ...

    @property
    def is_draw(self) -> bool:
        return (not self.is_win) and (len(self.legal_moves) == 0)

    @abstractmethod
    def evaluate(self, player: Piece) -> float:
        ...
```

`Move 타입`은 게임에서 이동을 나타내며, 정수 타입이다.

틱택토나 커넥트포 같은 게임에서는 말을 정수만큼 특정 위치로 이동할 수 있다.

`Piece 클래스`는 게임보드의 말에 대한 **기본 클래스**다.

또한 두 개의 턴 표시를 한다.

이 때문에 `opposite 속성`이 필요하다.

게임에서 턴은 *플레이어가 말을 놓을 수 있는 차례*를 말한다.

`Board 추상 클래스`는 **게임의 상태를 관리**한다.

탐색 알고리즘이 계산할 게임에 대해 우리는 **'누구 차례인가, 말은 현재 위치에서 어디로 움직일 수 있는가, 이겼는가, 무승부인가'** 와 같은 질문에 답할 수 있어야 한다.

**무승부**에 대한 마지막 질문은 *'말은 현재 위치에서 어디로 움직일 수 있는가, 이겼는가'* 의 질문을 조합한것으로, **게임에서 말이 이길 수 있는 움직임이 더 이상 없다**면 무승부이다.

`추상 클래스 Game`에는 이에 대응하는 `is_draw 속성`이 있다.

클래스의 나머지 두 조치로는 '현재 위치에서 새 위치로 이동한다, 플레이어의 말 위치를 평가하여 어느 쪽이 유리한지 확인한다'가 있다.

`Board 클래스`의 각 메서드와 속성은 이전에 언급한 네 가지 질문이나 두 조치에 대응한다.

`Board 클래스`는 게임 용어에 따라 `Position 클래스`로 명명할 수 있지만, 각 서브클래스에는 좀 더 구체적인 이름을 사용할 것이다.

___

### 8.2 틱택토

**틱택토**는 간단한 게임이지만, *커넥트포, 체커스, 체스*와 같은 **고급 전략 게임**에 적용되는 `최소최대 알고리즘`을 사용하여 구현할 수 있다.

**틱택토 게임**의 진행 상황을 추적하는 클래스를 구현하기 위해 먼저 *틱택토 보드의 각 말을 나타내는 방법*이 필요하다.

먼저 `TTTPiece 열거형 클래스`에서 `Piece 클래스`를 상속받는다.

틱택토 말은 *X, O, 빈공간*으로 표시된다.

```python
# tictactoe.py

from __future__ import annotations
from typing import List
from enum import Enum
from board import Piece, Board, Move

class TTTPiece(Piece, Enum):
    X = "X"
    O = "O"
    E = " " # 빈 공간

    @property
    def opposite(self) -> TTTPiece:
        if self == TTTPiece.X:
            return TTTPiece.O
        elif self == TTTPiece.O:
            return TTTPiece.X
        else:
            return TTTPiece.E

    def __str__(self) -> str:
        return self.value
```

`TTTPiece 클래스`에는 상대방의 `TTTPiece 클래스`를 반환하는 `opposite 속성`이 있다.

이것은 **틱택토**에서 한 플레이어의 말이 이동한 후, **다른 플레이어의 턴이 왔을 때** 사용한다.

보드에서 **말의 이동**을 표시하기 위해 *말이 놓일 보드의 사각형에 **해당하는 정수***를 사용하고, `board.py`에 `Move 타입`을 정수로 정의한다.

`TTTBoard 클래스`는 *게임 상태를 저장*하며, 서로 다른 두 말의 상태(위치, 플레이어 턴)를 추적한다.

`Board 클래스`의 생성자에는 X를 이동하여 위치를 초기화하는 기본 매개변수가 있다.

```python
# tictactoe.py

# TTTBoard 클래스는 비공식적으로 변경이 불가능한 자료구조, 객체를 수정해서는 안 된다
class TTTBoard(Board):
    def __init__(self, position: List[TTTPiece] = [TTTPiece.E] * 9, turn: TTTPiece = TTTPiece.X) -> None:
        self.position: List[TTTPiece] = position
        self._turn: TTTPiece = turn
        # turn 속성과 _turn 인스턴스 변수
        # : 모든 Board 클래스의 서브클래스가 자신의 턴을 추적할 수 있도록 하는 꼼수

    @property
    def turn(self) -> Piece:
        return self._turn

    def move(self, location: Move) -> Board:
        temp_position: List[TTTPiece] = self.position.copy()
        temp_position[location] = self._turn
        return TTTBoard(temp_position, self._turn.opposite)

    # 리스트 컴프리헨션을 사용하여 말이 이동할 수 있는 곳을 파악
    @property
    def legal_moves(self) -> List[Move]:
        return [Move(l) for l in range(len(self.position)) if self.position[l] == TTTPiece.E]

    @property
    def is_win(self) -> bool:
        # 3행, 3열, 2개의 대각선을 확인
        # 행과 열, 대각선의 위치가 모두 비어 있지 않고, 모두 같은 말이 놓여 있다면 게임에서 승리
        # 어느 한 줄에 같은 말이 놓여 있지 않고, 더 이상 움직일 곳이 없다면 무승부
        return self.position[0] == self.position[1] and self.position[0] == self.position[2] and self.position[0] != TTTPiece.E or \
        self.position[3] == self.position[4] and self.position[3] == self.position[5] and self.position[3] != TTTPiece.E or \
        self.position[6] == self.position[7] and self.position[6] == self.position[8] and self.position[6] != TTTPiece.E or \
        self.position[0] == self.position[3] and self.position[0] == self.position[6] and self.position[0] != TTTPiece.E or \
        self.position[1] == self.position[4] and self.position[1] == self.position[7] and self.position[1] != TTTPiece.E or \
        self.position[2] == self.position[5] and self.position[2] == self.position[8] and self.position[2] != TTTPiece.E or \
        self.position[0] == self.position[4] and self.position[0] == self.position[8] and self.position[0] != TTTPiece.E or \
        self.position[2] == self.position[4] and self.position[2] == self.position[6] and self.position[2] != TTTPiece.E

    # 플레이어가 이기면 한 숫자를 반환, 비기면 더 낮은 숫자를 반환, 지면 훨씬 더 낮은 숫자를 반환
    def evaluate(self, player: Piece) -> float:
        if self.is_win and self.turn == player:
            return -1
        elif self.is_win and self.turn != player:
            return 1
        else:
            return 0

    def __repr__(self) -> str:
        return f"""{self.position[0]}|{self.position[1]}|{self.position[2]}
-----
{self.position[3]}|{self.position[4]}|{self.position[5]}
-----
{self.position[6]}|{self.position[7]}|{self.position[8]}"""
```


**최소최대**는 *틱택토, 체커, 체스*와 같은 완벽한 정보를 가진 2인용 제로섬 게임에서 **최적의 이동을 찾는 고전 알고리즘**이다.

이 알고리즘은 다른 유형의 게임에서 수정 및 확장되어 사용된다.

`최소최대 알고리즘`은 각 플레이어가 **최대화 플레이어** 또는 **최소화 플레이어**로 지정된 *재귀 함수*를 사용하여 구현한다.

**최대화 플레이어**는 *최대 이익을 얻을 수 있는 **이동 목표***로 하고, *최소화 플레이어의 이동을 고려*해야 한다.

`최소최대 알고리즘`은 **최대화 플레이어의 이득을 최대화**하려는 시도 후 *재귀적으로 호출*되어 **상대방 최대화 플레이어의 이득을 최소화**하는 이동을 찾는다.

이는 *재귀 함수의 기저 조건에 도달*할 때까지 계속 *반복*되고, 기저 조건은 게임 종료 위치(승리 또는 무승부) 또는 최대 탐색 깊이다.

최소최대 알고리즘은 최대화 플레이어의 시작 위치에 대한 평가 점수를 반환한다.

`TTTBoard 클래스`의 `evaluate() 메서드`에서 플레이어가 최대화 플레이어에게 `이기면 1점`, `지면 -1점`, `비기면 0점`을 얻는다.

이 점수는 기저 조건에 도달하면 반환되는데, 이 기저 조건에 연결된 모든 재귀 호출을 통해 점수가 버블링된다.

최대화 작업에서 각 재귀 호출은 최고 평가를 위해 한 단계 더 버블링하는 과정을 반복하며 의사 결정 트리가 작성된다.

탐색 공간이 너무 커서 게임 종료 위치에 도달할 수 없는 게임의 경우까지 탐색 후 중단되고 그 다음 휴리스틱을 사용하여 게임 상태를 평가한다.

```python
# minimax.py

from __future__ import annotations
from board import Piece, Board, Move

# 게임 플레이어의 가능한 최선의 움직임을 찾는다.
def minimax(board: Board, maximizing: bool, original_player: Piece, max_depth: int = 8) -> float:
    # 기저 조건 - 게임 종료 위치 또는 최대 깊이에 도달
    if board.is_win or board.is_draw or max_depth == 0:
        return board.evaluate(original_player)

    # 재귀 조건 - 이익을 극대화하거나 상대방의 이익을 최소화
    if maximizing:
        best_eval: float = float("-inf") # 낮은 시작 점수
        for move in board.legal_moves:
            result: float = minimax(board.move(move), False, original_player, max_depth - 1)
            best_eval = max(result, best_eval) # 가장 높은 평가를 받은 위치로 움직인다.
        return best_eval
    else: # 최소화
        worst_eval: float = float("inf") # 높은 시작 점수
        for move in board.legal_moves:
            result = minimax(board.move(move), True, original_player, max_depth - 1)
            worst_eval = min(result, worst_eval) # 가장 낮은 평가를 받은 위치로 움직인다.
        return worst_eval

# 최대 깊이(max_depth) 전까지 최선의 움직임을 찾는다.
# 헬퍼 함수를 생성하여 각 유효한 이동에 대한 minimax()함수 호출을 반복하여 가장 높은 값으로 평가되는 이동을 찾는다.
# max_depth 변수의 기본값은 8이므로 틱택토 컴퓨터 플레이어는 항상 게임의 마지막 수까지 생각
def find_best_move(board: Board, max_depth: int = 8) -> Move:
    best_eval: float = float("-inf")
    best_move: Move = Move(-1)
    for move in board.legal_moves:
        result: float = minimax(board.move(move), False, board.turn, max_depth)
        if result > best_eval:
            best_eval = result
            best_move = move
    return best_move
```

틱택토는 주어진 위치에서 명확하고 올바른 움직임을 알아내기 쉬운 정말 간단한 게임으로, 단위 테스트 또한 쉽게 구현할 수 있다.

틱택토의 **세 가지 시나리오(다음 이동에 게임을 이기는 위치를 테스트, 상대방이 이기는 상황을 막는 테스트, 앞으로의 두 수를 생각해야 하는 조금 더 복잡한 테스트)를 테스트**한다.

```python
# tictactoe_tests.py

import unittest
from typing import List
from minimax import find_best_move
from tictactoe import TTTPiece, TTTBoard
from board import Move

class TTTMinimaxTestCase(unittest.TestCase):
    def test_easy_position(self):
        # 다음 턴에 X가 이겨야 한다.
        to_win_easy_position: List[TTTPiece] = [TTTPiece.X, TTTPiece.O, TTTPiece.X,
                                                TTTPiece.X, TTTPiece.E, TTTPiece.O,
                                                TTTPiece.E, TTTPiece.E, TTTPiece.O]
        test_board1: TTTBoard = TTTBoard(to_win_easy_position, TTTPiece.X)
        answer1: Move = find_best_move(test_board1)
        self.assertEqual(answer1, 6)

    def test_block_position(self):
        # O의 승리를 막아야 한다.
        to_block_position: List[TTTPiece] = [TTTPiece.X, TTTPiece.E, TTTPiece.E,
                                             TTTPiece.E, TTTPiece.E, TTTPiece.O,
                                             TTTPiece.E, TTTPiece.X, TTTPiece.O]
        test_board2: TTTBoard = TTTBoard(to_block_position, TTTPiece.X)
        answer2: Move = find_best_move(test_board2)
        self.assertEqual(answer2, 2)

    def test_hard_position(self):
        # 남은 두 턴을 고려해서 최선의 이동을 찾는다.
        to_win_hard_position: List[TTTPiece] = [TTTPiece.X, TTTPiece.E, TTTPiece.E,
                                                TTTPiece.E, TTTPiece.E, TTTPiece.O,
                                                TTTPiece.O, TTTPiece.X, TTTPiece.E]
        test_board3: TTTBoard = TTTBoard(to_win_hard_position, TTTPiece.X)
        answer3: Move = find_best_move(test_board3)
        self.assertEqual(answer3, 1)

if __name__ == '__main__':
    unittest.main()
```


틱택토 게임을 할 수 있는 컴퓨터 플레이어를 개발하는 것으로, 틱택토 AI는 테스트 위치를 평가하는 대신 상대방의 말의 움직임에 의해 생성된 위치만 평가하게 된다.

```python
# tictatoe_ai.py
# 틱택토 AI는 먼저 말을 두는 인간을 상대로 플레이

from minimax import find_best_move
from tictactoe import TTTBoard
from board import Move, Board

board: Board = TTTBoard()

def get_player_move() -> Move:
    player_move: Move = Move(-1)
    while player_move not in board.legal_moves:
        play: int = int(input("이동할 위치를 입력하세요 (0-8):"))
        player_move = Move(play)
    return player_move

if __name__ == "__main__":
    # 메인 게임 루프
    while True:
        human_move: Move = get_player_move()
        board = board.move(human_move)
        if board.is_win:
            print("당신이 이겼습니다!")
            break
        elif board.is_draw:
            print("비겼습니다!")
            break
        computer_move: Move = find_best_move(board)
        print(f"컴퓨터가 {computer_move}(으)로 이동했습니다.")
        board = board.move(computer_move)
        print(board)
        if board.is_win:
            print("컴퓨터가 이겼습니다!")
            break
        elif board.is_draw:
            print("비겼습니다!")
            break
```

```text
두 상대가 매 턴마다 최선의 이동을 하며, 완벽하게 알고리즘이 동작한다.
틱택토의 완벽한 게임 결과는 비기는 것으로, 컴퓨터 플레이어를 사람이 이길 수 없다.
```

___

### 8.3 커넥트포

`커넥트포`는 세워져 있는 **7x6 격자판에 두 명의 플레이어가 교대로 말을 두어 가로, 세로 또는 대각선 4개를 만들면 이기는 게임**이다.

플레어이는 매 턴마다 7개의 열 중 어디에 말을 둘지 결정한다.

`커넥트포`는 여러가지 면에서 **틱택토와 비슷**하다.

두 게임 모두 *격자 위에서 진행*되며, *플레이어가 이길 수 있도록* 말을 격자 위에 놓는다.

**커넥트포는 틱택토보다 격자판이 더 크고 이길 수 있는 방법이 많아서** 각 플레이어 말의 위치를 평가하는 것이 훨씬 더 복잡하다.

두 게임 모두 이 장의 시작부분에서 본 것과 동일한 `Piece 클래스`와 `Board 클래스`의 `서브클래스`로 구현되어 두 게임 모두에 `minimax() 함수`를 사용할 수 있다.

```python
# connectfour.py

from __future__ import annotations
from typing import List, Optional, Tuple
from enum import Enum
from board import Piece, Board, Move

class C4Piece(Piece, Enum): # TTTPiece 클래스와 거의 비슷
    B = "B"
    R = "R"
    E = " " # 빈 공간

    @property
    def opposite(self) -> C4Piece:
        if self == C4Piece.B:
            return C4Piece.R
        elif self == C4Piece.R:
            return C4Piece.B
        else:
            return C4Piece.E

    def __str__(self) -> str:
        return self.value

# 일정 크기의 커넥트포 격자에서 플레이어가 이기는 부분을 생성하는 함수
def generate_segments(num_columns: int, num_rows: int, segment_length: int) -> List[List[Tuple[int, int]]]:
    segments: List[List[Tuple[int, int]]] = []
    # 수직 세그먼트를 생성
    for c in range(num_columns):
        for r in range(num_rows - segment_length + 1):
            segment: List[Tuple[int, int]] = []
            for t in range(segment_length):
                segment.append((c, r + t))
            segments.append(segment)

    # 수평 세그먼트를 생성
    for c in range(num_columns - segment_length + 1):
        for r in range(num_rows):
            segment = []
            for t in range(segment_length):
                segment.append((c + t, r))
            segments.append(segment)

    # 왼쪽 아래에서 오른쪽 위 대각선의 세그먼트를 생성
    for c in range(num_columns - segment_length + 1):
        for r in range(num_rows - segment_length + 1):
            segment = []
            for t in range(segment_length):
                segment.append((c + t, r + t))
            segments.append(segment)

    # 왼쪽 위에서 오른쪽 아래 대각선의 세그먼트를 생성
    for c in range(num_columns - segment_length + 1):
        for r in range(segment_length - 1, num_rows):
            segment = []
            for t in range(segment_length):
                segment.append((c + t, r - t))
            segments.append(segment)
    return segments # 격자 위치 리스트의 리스트를 반환
    # 네 개의 격자 위치 리스트를 세그먼트라고 부른다


class C4Board(Board): # 지정한 크기의 보드에서 세그먼트를 SEGMENT 변수로 캐시
    NUM_ROWS: int = 6
    NUM_COLUMNS: int = 7 
    SEGMENT_LENGTH: int = 4
    SEGMENTS: List[List[Tuple[int, int]]] = generate_segments(NUM_COLUMNS, NUM_ROWS, SEGMENT_LENGTH)

    class Column: # 격자 표현에 비해 성능이 약각 저하될 수 있지만, 커넥트포 보드를 7개의 열 그룹으로 생각하면 개념적으로 이해하기 더 쉽다
        def __init__(self) -> None:
            self._container: List[C4Piece] = []

        @property
        def full(self) -> bool:
            return len(self._container) == C4Board.NUM_ROWS

        def push(self, item: C4Piece) -> None:
            if self.full:
                raise OverflowError("격자 열 범위에 벗어날 수 없습니다.")
            self._container.append(item)

        # Column 인스턴스를 인덱싱할 수 있어서 열 리스트를 2차원 리스트처럼 취급
        def __getitem__(self, index: int) -> C4Piece:
            if index > len(self._container) - 1:
                return C4Piece.E
            return self._container[index]

        def __repr__(self) -> str:
            return repr(self._container)

        def copy(self) -> C4Board.Column:
            temp: C4Board.Column = C4Board.Column()
            temp._container = self._container.copy()
            return temp

    def __init__(self, position: Optional[List[C4Board.Column]] = None, turn: C4Piece = C4Piece.B) -> None:
        if position is None:
            self.position: List[C4Board.Column] = [C4Board.Column() for _ in range(C4Board.NUM_COLUMNS)]
        else:
            self.position = position
        self._turn: C4Piece = turn

    @property
    def turn(self) -> Piece:
        return self._turn

    def move(self, location: Move) -> Board:
        temp_position: List[C4Board.Column] = self.position.copy()
        for c in range(C4Board.NUM_COLUMNS):
            temp_position[c] = self.position[c].copy()
        temp_position[location].push(self._turn)
        return C4Board(temp_position, self._turn.opposite)

    @property
    def legal_moves(self) -> List[Move]:
        return [Move(c) for c in range(C4Board.NUM_COLUMNS) if not self.position[c].full]

    # 헬퍼 메서드, 특정 세그먼트의 검은 말과 빨간 말의 수를 반환
    def _count_segment(self, segment: List[Tuple[int, int]]) -> Tuple[int, int]:
        black_count: int = 0
        red_count: int = 0
        for column, row in segment:
            if self.position[column][row] == C4Piece.B:
                black_count += 1
            elif self.position[column][row] == C4Piece.R:
                red_count += 1
        return black_count, red_count

    @property
    def is_win(self) -> bool: # 보드의 세그먼트를 모두 확인
        for segment in C4Board.SEGMENTS:
            black_count, red_count = self._count_segment(segment)
            if black_count == 4 or red_count == 4: # 승리 결정
                return True
        return False

    # 위치를 평가하기 위해 모든 대표 세그먼트를 한 번에 한 세그먼트씩 평가하고, 이를 합산하여 결과를 반환
   
    def _evaluate_segment(self, segment: List[Tuple[int, int]], player: Piece) -> float:
        black_count, red_count = self._count_segment(segment)
        if red_count > 0 and black_count > 0:
            return 0 # 말이 혼합된 세그먼트는 0점(빨간 말과 검은 말이 함께 있는 세그먼트)
        count: int = max(red_count, black_count)
        score: float = 0
        if count == 2: # 같은 색 말이 두 개와 빈공간 두개가 있는 세그먼트는 1점
            score = 1
        elif count == 3: # 같은 색 말 세 개가 있는 세그먼트는 100점
            score = 100
        elif count == 4: # 같은 색 말 4개가 있는 세그먼트는 1,000,000점
            score = 1000000
        color: C4Piece = C4Piece.B
        if red_count > black_count:
            color = C4Piece.R
        if color != player:
            return -score
        return score

    # _evaluate_segment() 메서드를 사용하여 모든 세그먼트의 총 점수를 반환
    def evaluate(self, player: Piece) -> float:
        total: float = 0
        for segment in C4Board.SEGMENTS:
            total += self._evaluate_segment(segment, player)
        return total

    def __repr__(self) -> str:
        display: str = ""
        for r in reversed(range(C4Board.NUM_ROWS)):
            display += "|"
            for c in range(C4Board.NUM_COLUMNS):
                display += f"{self.position[c][r]}" + "|"
            display += "\n"
        return display
```

틱택토에서 사용한 `minimax()`와 `find_best_move() 함수`를 커넥트포에서 똑같이 적용할 수 있다.

`틱택토 AI 코드`에서 몇 가지 사항만 변경하면 되는데, 가장 큰 차이점은 **max_depth 변수를 3으로 설정**한 것이다.

```python
# connectfour_ai.py

from minimax import find_best_move
from connectfour import C4Board
from board import Move, Board

board: Board = C4Board()

def get_player_move() -> Move:
    player_move: Move = Move(-1)
    while player_move not in board.legal_moves:
        play: int = int(input("이동할 열 위치를 입력하세요 (0-6):"))
        player_move = Move(play)
    return player_move

if __name__ == "__main__":
    # 메인 게임 루프
    while True:
        human_move: Move = get_player_move()
        board = board.move(human_move)
        if board.is_win:
            print("당신이 이겼습니다")
            break
        elif board.is_draw:
            print("비겼습니다!")
            break
        computer_move: Move = find_best_move(board, 5)
        print(f"컴퓨터가{computer_move}열을 선택습니다.")
        board = board.move(computer_move)
        print(board)
        if board.is_win:
            print("컴퓨터가 이겼습니다!")
            break
        elif board.is_draw:
            print("비겼습니다!")
            break
```
틱택토 AI와 달리 **플레이어 말이 움직이는 데 몇 초가 걸린다.**

**커넥트포 AI**는 명백한 실수를 하지 않아, 플레이어가 신중하게 움직이지 않는 한, **커넥트포 AI가 이길 것**이다.

탐색 깊이를 늘려서 AI 성능을 조금 더 높일 수 있지만, 컴퓨터 말의 이동마다 계산 시간이 기하급수적으로 늘어난다.

`최소최대 알고리즘`은 잘 작동하지만 **매우 깊은 탐색은 할 수 없다.**

하지만, **알파-베타 가지치기**를 이용해서 이미 탐색한 위치보다 점수가 낮은 위치를 제외시키면 **최소최대 알고리즘의 탐색 깊이를 향상**시킬 수 있다.

이는 `최소최대 알고리즘의 재귀 호출 과정`에서 알파와 베타의 두 가지 값을 추적하여 이뤄진다.

**알파**는 탐색 트리에서 현재까지 발견된 **최고의 최대화 움직임 평가**를 나타내고, **베타**는 상대방에 대해 현재까지 발견된 **최고의 최소화 움직임 평가**를 나타낸다.

```python
# minimax.py

def alphabeta(board: Board, maximizing: bool, original_player: Piece, max_depth: int = 8, alpha: float = float("-inf"), beta: float = float("inf")) -> float:
    # 기저 조건 - 종료 위치 또는 최대 깊이에 도달
    if board.is_win or board.is_draw or max_depth == 0:
        return board.evaluate(original_player)

    # 재귀 조건 - 자신의 이익을 최대화하거나 상대방의 이익을 최소화
    if maximizing: #최대화
        for move in board.legal_moves:
            result: float = alphabeta(board.move(move), False, original_player, max_depth - 1, alpha, beta)
            alpha = max(result, alpha)
            if beta <= alpha:
                break
        return alpha
    else:   # 최소화
        for move in board.legal_moves:
            result = alphabeta(board.move(move), True, original_player, max_depth - 1, alpha, beta)
            beta = min(result, beta)
            if beta <= alpha:
                break
        return beta
        
def find_best_move(board: Board, max_depth: int = 8) -> Move:
    best_eval: float = float("-inf")
    best_move: Move = Move(-1)
    for move in board.legal_moves:
        result: float = alphabeta(board.move(move), False, board.turn, max_depth)
        if result > best_eval:
            best_eval = result
            best_move = move
    return best_move
```
```text
탐색 깊이를 5로 설정하면, minimax() 함수를 사용하는 경우 컴퓨터가 말을 이동하는 데 약 3분이 걸리고, alphabeta() 함수를 사용하는 경우 약 30초로, 놀라운 성능 개선을 확인할 수 있다.
```

___

### 8.4 알파-베타 가지치기를 넘어서

이 장에서 사용된 알고리즘은 심도 있게 연구되었으며, 수년에 걸쳐서 많이 개선되었다.

한 예로 체스에서 *말이 합법적으로 움직이는 시간을 단축*하는 '**비트보드**'와 같은 기술이 있으며, 이는 *대부분의 게임에서 활용할 수 있는 일반적인 기술*이다.

일반적인 기술 중 하나는 **반복 심화**로, 반복 심화에서는 *탐색 함수가 먼저 최대 깊이 1로 실행*하고, 최대 깊이 2, 3.. 으로 계속 실행하다가 *지정된 시간제한에 걸리면 탐색을 중지하고 마지막으로 완료된 깊이의 결과를 반환*한다.

이 장의 예제는 특정 깊이로 하드 코딩되어, 게임 시간에 제한이 없어서 컴퓨터가 생각하는 데 시간이 오래 걸려도 된다. 

컴퓨터의 다음 움직임을 위해 *고정된 탐색 깊이와 가변적인 시간* 대신 **반복 심화**를 통해 **가변적인 탐색 깊이와 고정된 시간을 사용하는 AI**가 필요하다.

또 다른 개선 사항으로 **정점 탐색**이 있는데, 이 기술에서 *최소최대 탐색 트리는 상대적으로 정적인 위치의 경로가 아닌 큰 변화를 일으키는 경로에 따라 확장*된다.

**최소최대 탐색을 개선하는 가장 좋은 두 가지 방법**은 *할당된 시간 동안 더 깊이 탐색*하거나 *위치를 평가하는 데 사용되는 평가 함수를 개선*하는 것이다

같은 시간에 *더 많은 위치를 탐색*하려면 각 위치에서 *더 적은 시간을 소비*해야 한다.

코드 효율성을 높이거나 더 빠른 하드웨어를 사용해서 해결할 수 있지만, 최대한 각 위치의 평가를 향상시켜서 코드로 문제를 개선하는 게 더 좋다.

위치를 평가하기 위해 **더 많은 매개변수 또는 휴리스틱을 사용**하면 시간이 더 걸릴 수 있으나, 궁극적으로 더 좋은 움직임을 찾기 위해 **탐색 깊이가 더 적은 깊이를 요구하는 더 좋은 방향**으로 다음 턴이 이어질 수 있다.

____

### 8.5 적용사례

**알파-베타 가지치기**와 같은 `최소최대 알고리즘`의 확장 대부분은 가장 현대적인 **체스 게임 엔진의 기초**이다.

이들은 다양한 전략 게임에 성공적으로 적용되었고, 대부분의 보드게임 인공 상대는 아마도 어떤 형태의 **최소최대 알고리즘을 사용**할 것이다.

**최소최대 알고리즘**과 **알파-베타 가지치기**와 같은 확장 기술은 체스게임에서 매우 효과적이어서 1997년 체스 세계챔피언인 게리 가스파로프가 IBM이 만든 컴퓨터인 딥블루에 패배했다.

20년이 지난 후에도 대다수의 체스게임 엔진은 **최소최대 알고리즘을 기반**으로 하고, 오늘날의 최소최대 알고리즘 기반의 체스게임 엔진인 세계 최고의 체스 플레이어보다 훨씬 뛰어나다.

새로운 머신러닝 기술은 *순수한 최소최대 알고리즘 기반의 체스게임 엔진*에 도전하기 시작했지만 아직 *체스게임에서 우위를 확실하게 입증하지 못했다*.

게임의 분기 요소가 높을수록 최소최대 알고리즘의 효과는 떨어지는데, **분기 요인은 일부 게임의 특정 위치에서 발생 가능한 평균 이동 횟수**이고, 이것이 최근 보드게임 바둑의 *컴퓨터 플레이어의 진보가 머신러닝과 같은 다른 영역의 탐색을 요구하는 이유*이다.

턴 기반의 게임에서 새 보드게임의 **인공 상대를 구현**하는 경우 `최소최대 알고리즘`을 가장 먼저 생각해봐야 한다.

**최소최대 알고리즘**은 경제 및 정치 시뮬레이션뿐만 아니라 게임 이론 실험에도 사용할 수 있다.

**알파-베타 가지치기**는 모든 형태의 **최소최대 알고리즘**에 적용되어야 한다.

___

### 8.6 연습문제

#### (1)
**틱택토에 단위 테스트를 추가하여 legal_moves, is_win, is_draw 속성이 잘 작동하는지 확인하라.**

#### (2)
**커넥트포에 대한 최소최대 알고리즘의 단위 테스트를 작성하라.**

#### (3)
**tictactoe_ai.py와 connectfour_ai.py의 코드는 거의 비슷하다. 이 두 코드를 두 게임 모두에서 사용할 수 있도록 두 메서드로 작성하여 리팩토링하라.**

#### (4)
**컴퓨터 플레어이가 자신과 게임할 수 있도록 connectfour_ai.py 코드를 변경해보자. 첫 번째 플레이어와 두 번째 플레이어 중 누가 더 많이 이기는가? 매번 같은 선수가 이기는가?**
```python
from minimax import find_best_move
from connectfour import C4Board
from board import Move, Board

board: Board = C4Board()

def get_player_move() -> Move:
    player_move: Move = Move(-1)
    while player_move not in board.legal_moves:
        play: int = int(input("이동할 열 위치를 입력하세요 (0-6):"))
        player_move = Move(play)
    return player_move

if __name__ == "__main__":
    # 메인 게임 루프
    while True:
        print("첫 번째 플레이어 : ")
        human_move1: Move = get_player_move()
        board = board.move(human_move1)
        if board.is_win:
            print("첫번째 플레이어가 이겼습니다")
            break
        elif board.is_draw:
            print("비겼습니다!")
            break
        print("두 번째 플레이어 : ")
        human_move2: Move = get_player_move()
        board = board.move(human_move2)
        print(board)
        if board.is_win:
            print("두번째 플레이어가 이겼습니다!")
            break
        elif board.is_draw:
            print("비겼습니다!")
            break
```

#### (5)
**connectfour.py에서 평가 방법을 최적화하여 같은 시간 내에 더 높은 탐색 깊이를 가능하게 하는 방법을 찾아보라(기존 코드를 프로파일링하거나 다른 방법을 사용해도 좋다).**

#### (6)
**합법적인 체스 이동 생성 및 체스 게임 상태 유지 관리를 위해 이 장에서 개발한 alphabeta() 함수를 파이썬 라이브러리와 함께 체스 AI를 개발하라.**




___

## 참고 : 
고전 컴퓨터 알고리즘 인 파이썬(한빛미디어) - 데이비트 코펙 지음, 최길우 옮김 
