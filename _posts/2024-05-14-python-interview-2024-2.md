---
layout: post
title: "데이트팝 Python 주니어 개발자 인터뷰 후기 2탄"
author: Springkjw
categories: [Python, 채용, Datepop]
image: assets/images/coding-tests-for-interviews.jpg
featured: true
---

## 데이트팝 Python 주니어 개발자 인터뷰 후기

안녕하세요. 여러분에게 사랑을 전하는 데이트팝의 CTO, 권재원입니다.
지난 Python 주니어 개발자 채용 이후 최근 데이트팝에서 Python 주니어 개발자 채용 과정을 또 다시 진행하면서 경험한 기술 인터뷰의 후기를 공유하고자 합니다. 이 글이 개발자 취업을 준비하는 주니어 분들께 실질적인 도움이 되기를 바랍니다.

아래 인터뷰 질문은 데이트팝에서 채용을 위해 많은 고민을 하고 준비한 질문들입니다. 상업적 이용 이외에 학습을 위해 자유롭게 내용을 참고해주시고 공유해주시면 감사하겠습니다.

## 인터뷰 질문

이번 Python 채용에서는 파이썬 기초와 데이터베이스 이론에 대해 다루어보았습니다. 파이썬을 정말 사랑하고 차근차근 공부를 하였다면 무리 없이 답변할 수 있는 질문들 위주로 준비하였습니다.

### 파이썬의 Copy

파이썬에서 자주 다루는 Shallow Copy와 Deep Copy에 대해 다뤄보았습니다.

- 아래 코드를 실행 시키면 출력이 어떻게 될까요? 그 이유도 같이 설명해주세요.

```python
d1 = {"k": [1, 2, 3]}
d2 = d1

# 아래 출력의 결과는 그리고 그 이유는?
print(id(d1), id(d2))

d2["k"].append(99)
d3 = d1.copy()

# 아래 출력의 결과는 그리고 그 이유는?
print(id(d3))
print(d1, d2, d3)

import copy

d4 = copy.deepcopy(d1)
d3["k"].append(199)

# 아래 출력의 결과는 그리고 그 이유는?
print(id(d4))
print(d1, d2, d3, d4)
```

### 클래스 변수

파이썬에는 클래스 변수와 인스턴스 변수 개념이 있습니다. 두 개념을 정확히 이해한다면 쉽게 대답할 수 있는 질문입니다. 


```python
class Datepop:
  address = "Guro"
  members = []
  
instance1 = Datepop()
instance2 = Datepop()

instance2.address = "Seocho"
instance2.members.append("A")

# 아래 출력의 결과는 그리고 그 이유는 무엇인가요?
print(instance1.address, instance1.members)
```

### 제너레이터

```python
from sys import getsizeof

generator_object = (i for i in range(10000000))

my_list = [i for i in range(10000000)]

# 이터레이터와 제너레이터의 차이점은 무엇인가요?
# 두 출력의 차이는 무엇인가요? 그리고 그 이유는 무엇인가요?
print(getsizeof(generator_object))
print(getsizeof(my_list))
```

### 데이터베이스

데이터베이스를 공부한다면 빼놓을 수 없는 트랜잭션 개념에 대한 질문입니다.

- SOLID 원칙에 무엇인가요? SOLID 원칙은 왜 지켜야한다고 생각하실까요?

```python
from django.db import models
from django.db.models import F

class Counter(models.Model):
		id = models.IntegerField(unique=True, primary_key=True)
		count = models.IntegerField(default=0)

def increment(id):
    counter = Counter.objects.get(id=id)
    counter.count = counter.count + 1
    counter.save()

# Race Condition은 무엇인가요? 그리고 해결하기 위한 방법론으로 무엇이 있나요?
# 위의 코드에서 잘못 된 점은? 그리고 해결 방법은?
```

```python
from django.db import transaction

def create():
    with transaction.atomic():
        user = User.objects.create(name="jaewon", age=30)
        set_name(user.id)
        user.age = 31
        user.save()
 
def set_name(id):
    user = User.objects.get(id=id)
    user.name = "donghae"
    user.save()

# 위의 코드에서 잘못 된 점은 무엇인가요? 그리고 해결 방법은 무엇인가요?
```

```python
from django.db import transaction

class UserService:
    def transfer_money(self, from_user_id, to_user_id, amount):
        with transaction.atomic():
            from_user = User.objects.select_for_update().get(id=from_user_id)
            to_user = User.objects.select_for_update().get(id=to_user_id)

            if from_user.balance < amount:
                raise InsufficientFundsError()

            from_user.balance -= amount
            from_user.save()

            to_user.balance += amount
            to_user.save()

    def transfer_money_between_users(self, user1_id, user2_id, amount):
        with transaction.atomic():
            self.transfer_money(user1_id, user2_id, amount + 1000)
	          self.transfer_money(user2_id, user1_id, amount - 1000)

# 데드락은 무엇인가요? 해결하기 위해서는 어떻게 하면 되나요?
# 위의 코드에서 잘못된 점은 무엇인가요? 그리고 해결 방법은 무엇인가요?
```