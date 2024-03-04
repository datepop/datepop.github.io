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
최근 데이트팝에서 Python 주니어 개발자 채용 과정을 진행하면서 경험한 기술 인터뷰의 후기를 공유하고자 합니다. 이 글이 개발자 취업을 준비하는 주니어들에게 어떤 준비가 필요한지, 그리고 회사가 기대하는 Python 주니어 개발자의 모습에 대해 이해하는 데 도움이 되기를 바랍니다.

## 데이트팝은 어떤 개발자를 찾고 있었나요?

데이트팝에서는 기본기가 탄탄한 주니어 개발자를 찾고 있습니다. 난이도가 높은 질문도 있었지만, 중점을 둔 것은 정확한 답변이 아니라, 지금까지의 학습을 바탕으로 합리적인 추론을 할 수 있는가였습니다. 깊이 있는 공부를 한 사람이라면 기술 인터뷰를 수월하게 통과할 수 있었을 것입니다.

## 인터뷰 질문

### 파이썬의 Garbage Collection은 메모리를 어떻게 관리할까요?

이 질문은 Python의 Garbage Collection 메커니즘과 Reference Counting 방식에 대한 이해를 확인하기 위한 것이었습니다. Circular Reference가 발생할 때 메모리 누수가 왜 발생하는지 코드 리뷰를 통해 토론하였습니다.

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

주니어 개발자에게는 CS 기초와 함께 알고리즘에 대한 이해도 중요하다고 봅니다. 실무에서 바로 적용되지 않더라도, 기본적인 알고리즘 지식은 커리어를 쌓아가는 데 큰 차이를 만듭니다.
이번 알고리즘 라이브코딩 테스트는 어려운 문제는 아닌 간단한 동적 프로그래밍에 대한 지식을 확인하기 위한 문제를 제출하였습니다.

```
사람은 한번에 한 칸 혹은 두 칸의 계단을 오를 수 있다. 계단을 오르는데 필요한 비용(cost)이 있다고 가정할 때, 꼭대기까지 오를 수 있는 최소비용을 얼마인가?
```

## 기술 인터뷰 후기

여러분께서 데이트팝에 많은 관심을 가져주신 덕분에 이번 Python 주니어 개발자 채용에 훌륭한 분들이 많이 지원을 해주셨습니다. 100명이 넘는 지원자분들의 서류를 검토하고 기술 면접을 진행하면서 많은 분들과 이야기를 나눌 수 있어 저 역시 한 단계 더 성장할 수 있는 기회가 되었던 것 같습니다. 이번 기술 면접에서 특히 많은 분들이 `자신의 포트폴리오나 공부한 내용에 대해 피드백을 부탁드린다`라는 질문을 많이 하셨습니다. 서류 검토에서 많은 분들의 깃허브나 개발 블로그를 보면서 정말 많이 준비하시고 좋은 개발자가 되기 위해 많은 노력을 하고 있음을 알게 되었습니다. 당장 눈에 보이는 프로젝트 결과물이나 프레임워크에 대한 공부보다는 CS나 알고리즘 위주로 기본기를 다지면서 꾸준히 코딩을 하다보면 좋은 결과가 있을거라 믿습니다. 모든 분들을 다 모시고 싶었지만 회사의 사정으로 다 모실 수 없었던 점이 정말 아쉬웠고 다음 채용에서도 많은 관심을 가져주셨으면 합니다. 감사합니다.
