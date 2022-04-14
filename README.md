# datepop.github.io
Datepop Tech Blog

## Install Hugo

### Mac
```
brew install hugo
```

#### Git clone


## Run server

### Dev server

Draft 포스트가 보임
```
hugo server -D
```


### Production
```
hugo server
```


## 설정하기

### 기본 설정파일
config.toml에서 사이트 설명과 테마 설정을 한다

### 포스트 작성
아래 커맨드를 통해 생성하거나 직접 폴더 내부에 md 파일 생성
```
hugo new posts/my-first-post.md
```

### 포스트 템플릿
archetypes/default.md가 기본 템플릿이라서 수정이 필요하면 이 파일 수정

