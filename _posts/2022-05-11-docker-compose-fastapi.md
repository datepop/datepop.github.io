---
layout: post
title:  "가상환경 설정없이 Docker Compose로 FastAPI 개발환경 만들기"
author: Changyun
categories: [ Docker, Docker Compose, FastAPI ]
image: assets/images/fastapi.png
featured: true
---

## 가상환경 설정없이 Docker Compose로 FastAPI 개발환경 만들기

### 간단 FastAPI 설정

구조는 아래처럼 기본 구성
```
.
├── app
│   ├── __init__.py
│   └── main.py
├── Dockerfile
├── docker-compose.yml
└── requirements.txt
```

 - requirements.txt

```
fastapi>=0.68.0,<0.69.0
pydantic>=1.8.0,<2.0.0
uvicorn>=0.15.0,<0.16.0
```

 - app/__init__.py 는 빈파일
 - app/main.py

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}
```


### Dockerfile 만들기

python3.10 기준으로 만들기 위해서 3.10 이미지를 사용하고 아래 두줄은 만약 도커로 배포를 한다면 주석해제하고 사용하면 된다.

도커의 역할은 3.10 파이썬 이미지로 필요한 라이브러리를 설치하는 것!

```yml
FROM python:3.10

WORKDIR /code

COPY ./requirements.txt /code/requirements.txt
 
RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt
 
#COPY ./app /code/app
 
#CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]
```

### Docker Compose 만들기

도커로 python3.10에 필요한 라이브러리 (FastAPI 등등) 설치가 끝났으니 로컬 파일로만 실행해주면 된다.

그럼 local PC 에디터에서 수정하면 바로 반영이 되어 확인이 가능하다.

```yml
version: "3.7"

services:
  fastapi:
    image: fastapi
    command: uvicorn app.main:app --host 0.0.0.0 --port 8080 --reload
    ports:
      - 8080:8080
    volumes:
      - ./app:/code/app
```

설정이 비교적 많지않다. 원하는 포트 설정을 해주면되고 uvicorn에서는 파일 변경이 일어나면 재시작을 하기 위해 --reload를 넣어준다.

volumns에서는 app폴더에서 수정이 이뤄지면 컨테이너 code/app에도 연결되어 반영되게하기 위해 ./app:/code/app으로 설정한다.

설정은 -끝-

### 사용하기

#### Docker build 하기

```
docker build -t fastapi .
```

![docker-build]({{site.url}}/assets/images/posts/docker-compose-fastapi/docker-build.png)


#### 컨테이너 띄우기

```
docker-compose up
```

![docker-compose]({{site.url}}/assets/images/posts/docker-compose-fastapi/docker-compose.png)

#### localhost에서 확인하기

예제에서는 8080 포트로 설정했기 때문에 [http://localhost:8080](http://localhost:8080)에 들어가서 확인해본다.

![localhost]({{site.url}}/assets/images/posts/docker-compose-fastapi/localhost.png)

#### 파일 수정해보기

app/main.py에서 World를 World!로 수정해보면 .. 아래와같이 file detect하고 재시작하는것을 볼 수 있다.

![fastapi-reload]({{site.url}}/assets/images/posts/docker-compose-fastapi/fastapi-reload.png)


참고 문서] [https://fastapi.tiangolo.com/deployment/docker/](https://fastapi.tiangolo.com/deployment/docker/)