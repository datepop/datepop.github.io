---
layout: post
title:  "채용 사전과제 도입기 #1"
author: Changyun
categories: [ 채용, 과제 ]
image: assets/images/coding-tests-for-interviews.jpg
featured: true
# rating: 5
---

서류와 면접을 봐도 어느정도 판단은 가능하지만 정확하게 어느정도 개발을 하고 실력이 있는지는 알기 어렵다. 신입의 **개발 역량**과 경력직의 개발 이해도, 속도, 역량을 판단하기 위해 도입하기로 했다.

### 채용 위한 프로그래밍 과제 도입 #1

생성일: 2021년 12월 29일 오전 10:49

#### 도입 배경

서류와 면접을 봐도 어느정도 판단은 가능하지만 정확하게 어느정도 개발을 하고 실력이 있는지는 알기 어렵다. 신입의 **개발 역량**과 경력직의 개발 이해도, 속도, 역량을 판단하기 위해 도입하기로 했다.

##### 과제에 대한 고민

막상 과제를 내야겠다 생각했지만 인터넷에 있는 과제나 프로그래머스 등의 업체를 이용해서 하는게 아닌 이상 우리가 문제도 만들어야한다. 안드로이드, iOS, backend, frontend 등 각 분야에서의 프레임워크를 이용한 과제의 템플릿과 수행해야하는 문제들을 만들어야한다.

어떤 과제를 만들어도 상관은 없지만 우리가 가지고 있는 서비스와 비슷하고 고민하는 영역을 문제로 만들면 좋겠다는 생각이 들었다. 매번 다르겠지만 안드로이드, iOS는 API 조회와 그 결과를 애니메이션을 이용해서 보여주는거나 backend의 경우 데이트코스와 데이트스팟의 관계를 생성, 수정, 삭제하는 API 구현이 우선 떠오른다.

첫번째 과제처럼 프레임워크를 이용한 과제의 문제점은 우리와 다른 프레임워크를 오래 사용한 사람에 대한 평가를 하기는 어렵다는 것이다. 이런 경우 그 프레임워크에 맞춰서 과제를 주거나 알고리즘 또는 자료구조 문제를 내는 방향이 있다.

현재 모든 포지션 채용 진행중이지 않으니 채용 진행중인 **포지션**에 대해 과제를 우선 만들고 **자료구조**에 대한 과제를 먼저 만들기로 했다.

#### 자료구조 과제

최소한 Stack, Queue의 구현체는 구현해봐야한다는 생각에 이 2개 구현하는 것을 **기본 문제**로하고 나머지는 여기에서 확장하는 문제를 **보너스(optional)문제**로 출제했다.

파이썬 객체로 skeleton code를 만들어서 채워넣어야 하는 영역을 보여주도록 했다. (대학 과제처럼)

```python
from typing import Union

InputType = Union[int, str, float]

class Stack:
    def __init__(self) -> None:
        self.stack: list[InputType] = []

    def push(self, data: InputType) -> None:
        pass

    def pop(self) -> None:
        pass

    def display(self) -> list[InputType]:
        return self.stack

    def peek(self) -> InputType:
        pass
```

그리고 위의 코드를 통해 제대로 완성했는지 테스트를 하기 위해 unit test도 추가했다.

```python
import unittest

from stack.__init__ import Stack

class StackTestCase(unittest.TestCase):
    def setUp(self) -> None:
        return super().setUp()

    def test_stack_push(self):
        s = Stack()
        s.push(1)
        s.push(2)
        s.push(3)
        s.push(4)

        self.assertEqual(s.display(), [1,2,3,4])

    def test_stack_pop(self):
        pass

    def test_stack_peek(self):
        pass
```

stack에서 push에 대한 테스트코드는 작성해두고 나머지에 대한 테스트 코드는 지원자가 작성해보는것으로 열어놨다.

이렇게 각 주제별 폴더내에 test 파일을 만들어두면 최상위 폴더 위치에서 `python -m unittest`를 하면 모든 테스트를 한번에 돌릴 수 있고 다 정상통과했는지 또한 확인이 가능하다.

#### 과제 제출방식

크게 과제를 제공해주는 방식과 과제를 제출(받는)하는 방식이 있다.

1. 과제 제공
    1. 문제를 압축해서 이메일로 보내기
    2. Github에 private repository에 collaborator로 추가하기
2. 과제 제출
    1. 압축 푼 내용에 git init해서 지원자의 Github에 올린 후에 collaborator로 추가받기
    2. repository를 fork한 후 과제 commit하고 pull request하기
    3. 똑같이 이메일로 완성한 과제 압축해서 받기


위의 방법말고도 더 많이 있을거같은데 밑줄 친 방식으로 진행하기로 했다. 현재 방법에서는 Github에서 pull request를 하면 다른 지원자의 pull request를 볼 수 있을거같아 다른 방법을 못찾아서 이렇게 우선 해보려고 한다.

##### 절차 요약

1. 지원자에게 과제 압축파일을 이메일에 설명과 함께 첨부해서 보낸다.
2. 지원자가 과제 완성 후에 collaborator로 나를 추가한다.
3. collaborator로 추가된 것을 확인 후에 코드 내용을 확인한다.


다음에는 자료구조말고 실제 안드로이드 또는 백엔드 과제를 만드는 과정을 포스팅 예정이다.

끝.