---
title: Python Enum 클래스
date: 2019-08-10 20:35:38
updated:
categories:
  - 프로그래밍
tags:
  - Python
  - Enum
---

## 개요

현실의 문제를 프로그래밍으로 다루려고 고민하다 보면 조건에 따른 분기를 어떻게 처리할 것인지에 대해 생각하게 된다.
이 때 단순하고 전통적인 방식으로 조건을 확인하고 맞으면 이거, 아니면 저거 식의 if-else문을 사용해도 되지만
더 나은 프로그래밍 방식에 대한 가치관을 세우고 객체지향, 함수형 등의 다양한 개념을 접하다 보면
사람에 따라, 상황에 따라 열거형 클래스 등을 이용해서 처리하기도 한다.

Python에서는 3.4 버전부터 열거형 클래스인 Enum을 기본으로 탑재하였다.
이 문서에서는 이것을 사용하는 방법을 정리한다.

<!-- more -->

## 사용법

### 기본

```python
import enum

class Color(enum.Enum):
    RED = 0
    GREEN = 1
    BLUE = 2
```

`enum.Enum` 클래스를 상속받는 `Color` 클래스는 이제 열거형 상수로 이용된다.
이 예제에서는 정수를 이용하나 정수 뿐만 아니라 모든 자료형을 사용할 수 있다.

기본적인 사용에서는 다음과 같은 결과를 가져올 수 있다.
각 코드에 대한 반환값은 다음 줄에 주석으로 표기하였다.

```python
print(Color)
# <enum 'Color'>

print(Color.RED, Color.BLUE, Color.GREEN)
# Color.RED Color.BLUE Color.GREEN

print(Color.RED.name)
# RED

print(Color.RED.value)
# 0

print(repr(Color.RED))
# <Color.RED: 0>

print(type(Color.RED))
# <enum 'Color'>

print(Color(0), Color(2))
# Color.RED Color.BLUE

print(Color['RED'], Color['BLUE'])
# Color.RED Color.BLUE

print(Color.RED == Color.RED)
# True

print(Color.RED == 0)
# False
## enum을 정수와 비교하는 것은 항상 False

for c in Color:
    print(c)
# Color.RED
# Color.GREEN
# Color.BLUE

print(list(Color))
# [<Color.RED: 0>, <Color.GREEN: 1>, <Color.BLUE: 2>]
```

### Unique

열거형 데이터의 인덱스에 대한 유일성을 보장해야 하는 경우가 있을 수 있다.

```python
@enum.unique
class Foods(enum.Enum):
    SNACK = 0
    COKE = 1
    CIDER = 1
    JUICE = 1
    PIZZA = 2
    BURGER = 2
```

`enum.unique`라는 Decoration을 사용하면 위와 같이 중복된 값을 가지는 속성이 있는 경우 에러를 발생시킨다.

### Auto

Python 3.6 버전 이상에서는 `enum.auto()` 함수를 이용하여 자동으로 인덱스를 할당할 수 있다.

```python
class NewColor(enum.Enum):
    RED = enum.auto()
    GREEN = enum.auto()
    BLUE = enum.auto()
```

인덱스는 1부터 시작하고 1씩 증가한다.

## 참고자료

- [enum 클래스](https://pythonkim.tistory.com/90)
