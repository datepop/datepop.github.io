---
title: "Django에서 파일크기 체크 Decorator"
date: 2022-01-18T19:11:14+09:00
description: "Django에서 파일크기 체크 Decorator"
type: "post"
image: "/posts/default/python-logo.png"
categories: 
  - "Django"
tags:
  - "Django"
  - "Python"
  - "Decorator"
draft: false
toc: true
---

##### 파일 업로드 과정에서의 문제

프론트(웹 브라우저, 안드로이드, iOS)에서 리사이즈를 하더라도 업로드 하는 과정에 용량이 큰 파일들이 존재함. 프론트에서 전부다 해결을 해서 보낸다고 하더라도 서버에서 최종적으로 확인할 필요가 있음.

##### 필요한 기능?

우선 request에서 FILE을 읽고 거기에서 용량 체크하고 .. 최대 허용 크기 이상이면 에러 내고 ..

이렇게 생각하다보니 공통적으로 여러 업로드 api에서 쓰일수 있고 코드를 데코레이터(decorator)를 이용하면 간결하게 관리 할 수 있겠다고 생각함.

##### Decorator란?

함수가 또 다른 함수를 리턴하는 것. (@wrapper를 이용해서)

일반적으로 가장 많이 쓰이는 데코레이터는 classmethod() 와 staticmethod() 이다.

Decorator 구문은 단지 **syntactic sugar** (간결하고 보기 좋은 문법)

```python

def f(...):
    ...
f = staticmethod(f)

```
와 아래의 코드는 동일함.

```python

@staticmethod
def f(...):
    ...

```

위의 예제를 예로 들면 데코레이터를 구현하기 위해서는 staticmethod라는 함수를 만들어야한다.


##### 파일 용량 체크 데코레이터 만들기


아래처럼 decorator 함수를 만들수 있다. 데코레이터의 입력값이 없는 예제가 구글링하면 많아서 따라하기 편하고 좋은데 입력값이 있다면 복잡해진다 ㅠㅠ

```python

def my_decorator(input):
    def wrapper(func):
        @wraps(func)
        def decorator(request, *args, **kwargs):
            #
            # DO SOMETHING HERE
            #
            return func(request, *args, **kwargs) # 데코레이팅(?)된 함수 실행
        return decorator
    return wrapper

```

기본 입력값이 있는 데코레이터의 형식은 위와 같다. decorator 함수 아래에 원하는 로직을 넣으면 된다. 장고 api의 데코레이터라 request를 받고 진행된다.

실제 예제로 request로 입력을 받고 거기에서 FILE 가지고 용량 체크하는 로직을 넣었다.

```python

def limit_file_size(max_size): # 여기에서 입력값을 받을 수 있음. max_size
    def wrapper(func):
        @wraps(func)
        def decorator(request, *args, **kwargs):
            files = request.FILES

            for k in files.keys():
                if files[k].size > max_size: # 여기에서 입력값 max_size 사용
                    return "error"
            return func(request, *args, **kwargs) # 데코레이팅(?)된 함수 실행
        return decorator
    return wrapper

```

사용하는 곳에서는 아래와 같이 편리하게 사용 가능함. 코드도 더 직관적이고 간결해짐.

```python

@limit_file_size(10 * 1024 * 1024) # 입력값이 위에서의 max_size에 대응
def upload_file(request):
    return "good"

```


끝.