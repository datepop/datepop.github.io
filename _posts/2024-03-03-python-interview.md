---
layout: post
title: "데이트팝 Python 주니어 개발자 인터뷰 후기"
author: Springkjw
categories: [Python, 채용, Datepop]
image: assets/images/coding-tests-for-interviews.jpg
featured: true
---

## 데이트팝 Python 주니어 개발자 인터뷰 후기

안녕하세요. 여러분에게 사랑을 전하는 데이트팝의 CTO, 권재원입니다.
최근 데이트팝에서 Python 주니어 개발자 채용 과정을 진행하면서 경험한 기술 인터뷰의 후기를 공유하고자 합니다. 이 글이 개발자 취업을 준비하는 주니어 분들께 실질적인 도움이 되기를 바랍니다. 어떤 역량을 갖추어야 하는지, 그리고 데이트팝이 기대하는 Python 주니어 개발자상에 대해 함께 살펴보겠습니다.

## 데이트팝이 바라본 이상적인 주니어 개발자상

데이트팝에서는 탄탄한 기본기를 갖춘 주니어 개발자를 찾고 있습니다. 기술 인터뷰에서 난이도 높은 질문도 있었지만, 중점을 둔 것은 단순히 정답을 아는지의 여부가 아니었습니다. 그보다는 지금까지 학습한 내용을 바탕으로 문제를 이해하고 논리적으로 사고하는 역량을 중요하게 평가하였습니다. 컴퓨터 공학 전반에 걸친 깊이있는 공부를 한 사람이라면 기술 인터뷰를 무난히 통과할 수 있었을 것입니다.

## 인터뷰 질문

### 파이썬의 Garbage Collection과 메모리 관리

파이썬의 가비지 컬렉션 메커니즘과 참조 카운팅(Reference Counting) 방식에 대한 이해도를 평가하기 위한 질문이었습니다. 특히 순환 참조(Circular Reference)로 인한 메모리 누수가 발생하는 원인을 코드 리뷰를 통해 확인하였습니다.

- 아래 코드를 실행 시키면 출력이 어떻게 될까요? 그 이유도 같이 설명해주세요.

```python
class A:
    def __init__(self):
        pass

    def __del__(self):
        print("deleted")

def main():
    a = A()
    print("end")

if __name__ == "__main__":
    # 출력이 어떻게 될까요?
    main()
```

- 아래 코드를 실행 시키면 출력이 어떻게 될까요? 그 이유도 같이 설명해주세요.

```python
class A:
    def __init__(self):
        pass

    def __del__(self):
        print("delete")

def main():
    a = A()
    del a
    print("end")

if __name__ == "__main__":
    # 출력이 어떻게 될까요?
    main()
```

- 아래 코드를 실행 시키면 출력이 어떻게 될까요? 해당 코드는 어떤 문제점을 가지고 있을까요? 그 이유도 같이 설명해주세요.

```python
class A:
    def __init__(self, name, parent=None):
        self.name = name
        self.parent = parent
        self.children = set()

    def __del__(self):
        print("delete", self.name)

def main():
    a = A(name=1)
    a.children.add(A(name=2, parent=a))
    print("end")

if __name__ == "__main__":
    main()
```

- 아래 코드는 어떤 문제점을 가지고 있을까요? 메모리 누수가 일어난다고 하면 얼마나 일어날까요? 그 이유도 같이 설명해주세요.

```python
class A:
    def __init__(self, name, parent=None):
        self.name = name
        self.parent = parent
        self.children = set()
        self.workload = ' ' * 128 * 1024 * 1024

    def __del__(self):
        print("delete", self.name)

def main():
    for _ in range(10):
        a = A(name=1)
        a.children.add(A(name=2, parent=a))

    print("end")


if __name__ == "__main__":
    main()
```

### Django Queryset에 대한 이해도

Django를 사용한 경험이 있는 지원자를 대상으로, Queryset의 Lazy evaluation에 대한 이해를 확인하는 질문을 하였습니다.

- Django에서 아래 코드를 실행 시키면 실제 evaluated 되는 시점은 언제인가요?

```python
queryset = User.objects.filter(id=1234)

new_queryset = queryset

for q in queryset:
    print(q)

for q in queryset:
    print(q)
```

- 아래 코드의 차이점은 무엇일까요? 왜 성능차이가 발생하는 걸까요?

```python
queryset.count() vs len(queryset)
```

### 알고리즘 라이브코딩 테스트

기초적인 동적 프로그래밍 문제를 통해 알고리즘에 대한 기본기를 확인하였습니다. 알고리즘과 자료구조가 실무에 당장 사용되는 경우는 드물 수 있습니다. 하지만 개발자의 성장을 위해서는 반드시 갖추어야 할 기본 소양이라고 판단합니다.

```
사람은 한번에 한 칸 혹은 두 칸의 계단을 오를 수 있다. 계단을 오르는데 필요한 비용(cost)이 있다고 가정할 때, 꼭대기까지 오를 수 있는 최소비용을 얼마인가?
```

## 기술 인터뷰를 마무리하며

이번 채용에는 정말 많은 분들이 지원해 주셨습니다. 많은 지원자분들과 기술 인터뷰를 진행하면서 저 역시 크게 배우고 성장할 수 있었던 소중한 시간이었습니다. 특히 인상 깊었던 점은, 지원자분들 대부분이 자신의 포트폴리오와 그간 공부해온 내용에 대한 피드백을 적극적으로 요청하셨다는 것입니다. 끊임없이 개발 공부를 하고 열정적으로 준비하고 계신 모습을 볼 수 있었고, 더 나은 개발자로 성장하기 위해 끊임없이 노력하시는 자세가 매우 인상적이었습니다.
다만 한 가지 피드백을 드리자면, 화려한 프로젝트나 최신 기술 트렌드도 중요하지만 그에 앞서 CS 지식이나 알고리즘 등 기본기를 탄탄히 다지는 것이 무엇보다 중요하다는 점입니다. 기초가 단단해야 어떤 기술도 빠르게 습득하고 활용할 수 있기 때문입니다. 비록 모든 분들과 함께 하지는 못했지만, 앞으로도 데이트팝의 성장에 관심을 가져 주시고 응원해 주시면 감사하겠습니다. 다음 기회에 또 뵙기를 기대하겠습니다. 감사합니다.
