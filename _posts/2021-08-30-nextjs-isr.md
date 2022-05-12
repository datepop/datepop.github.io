---
layout: post
title:  "Nextjs ISR 시작하기"
author: Changyun
categories: [ Frontend, Nextjs ]
image: assets/images/nextjs.png
featured: false
---

Next.js는 Node.js 위에서 React 기반 웹을 server-side 렌더링과 정적 웹사이트 생성해주는 오픈소스이다.

## 서버사이드 렌더링 nextjs 도입하기

생성일: 2021년 8월 30일 오전 9:12

#### Nextjs?

Next.js는 Node.js 위에서 React 기반 웹을 server-side 렌더링과 정적 웹사이트 생성해주는 오픈소스이다. 리액트 공식문서에서도 언급하며 server-side 렌더링을 위해서 Next.js를 추천한다고 한다. 기존 React는 모든 내용을 client-side 브라우저에서 렌더했지만, Next.js는 기능을 확장해 server-side 렌더도 추가했다.

- 서버사이드 렌더링 사용하는 이유
    - 사용자에게 더 좋은 성능
    - 일관적은 SEO 성능

    서버사이드 렌더링에 대한 자세한 내용과 비교했을 때의 장점은 아래 블로그를 참고하기

    [The Benefits of Server Side Rendering Over Client Side Rendering](https://medium.com/walmartglobaltech/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8)

#### 지금까지의 Nextjs 역사

- 2016년 10월 25일 (초기)
6가지의 원칙을 가지고 시작했다.
    1. out-of-the box functionality requiring no setup
    2. JavaScript everywhere, all functions are written in JavaScript
    3. automatic code-splitting and server-rendering
    4. configurable data-fetching
    5. anticipating requests
    6. simplifying deployment
- 2018년 9월 (Version 7.0)
에러 핸들링 개선, React's context API 지원해서 dynamic route handling 개선
- 2019년 2월 (Version 8.0)
서버리스 배포 제공
- 2020년 3월 (Version 9.3)
글로벌 Sass, CSS Module 지원
- 2020년 7월 27일 (Version 9.5)
incremental static regeneration, rewrites, redirect support 추가

** 여기서 **Next.js 도입 배경**과 최근에 추가된 **incremental static regeneration** 적용한 경험을 공유하려고 한다.

#### 왜 도입했는가?

처음에는 사용해본 적이 없는 기술이기 때문에 위의 내용처럼 서버사이드 렌더링이 뭔지 실제로 사용해보며 경험하고 싶었고 다른 회사에서도 다 기본적으로 리액트(클라이언트사이드)말고 Next.js 서버사이드 렌더링을 사용한다고 해서 도입하고 싶었다.

예전에 php나 django template에서 사용한것처럼 서버에서 html을 내려주며 작동하던 웹사이트와 리액트의 장점이 잘 조화되면 좋을것 같아 최종 도입하게 되었다.

#### 설치, 설정, 배포

**설치**는 typescript가 추가된 create-next-app을 이용했다.

```
npx create-next-app --typescript
# or
yarn create next-app --typescript
```

**설정**은 추가적으로 eslint, prettier 설정을 했다. 나중에 알고 적용했는데 eslint 설정(.eslintrc 파일) extends에 next를 추가해주면 next에 맞는 lint 에러를 출력해준다.

**배포**는 yarn build 후 yarn start하는 도커를 만들어 쿠버네티스를 통해 배포했다.

### ISR(Incremental Static Regeneration)

SSR에서 페이지를 빌드하고 만들어진 html을 그대로 보여주는것의 문제는 배포를 하기전에 모든 페이지에 대해 빌드를 하고 배포해야한다.

데이트팝에 상품이 1000개가 있다면 1,000개의 페이지를 빌드 한 후 배포를 해야한다. 실제로 가능하지만 만약에 10,000개라면 빌드하는 시간만 엄청 오래 걸리고 효율적이지 못하다.

ISR은 빌드를 많이하면 좋지만 시간이 많이 걸리는 이슈를 해결하는 방법이다. 원하는 만큼의 페이지를 빌드한 다음 배포를 하고 배포 이후에 빌드되지 않은 url로 접근한다면 그때 그 페이지를 캐싱해서 저장한다. 사람들이 많이 접근하는 1000개의 상품의 페이지만 빌드하고 배포하고 시간이 흐름에 따라 1100개, 1500개까지 캐싱된 페이지가 증가 할 수도 있다.

아래 그림처럼 **A**처럼 꼭 필요한 페이지를 먼저 빌드하고 배포하고 **B**처럼 캐시도 많이 할 수 있다.

![Untitled](/posts/nextjs-isr/Untitled.png)

캐싱하는 주기(revalidate)도 설정이 가능하다. 0초에 v1의 페이지가 만들어지면 60초라는 시간 이전에는 v1의 페이지를 사용자에게 그대로 보여준다. 주기 60초 이후에 새로운 페이지 v2를 만들고 업데이트를 한다.

사용자의 요청 기준으로 throttle 방식으로 작동하는 것 같다. 아래 그림 참고.

![Untitled](/posts/nextjs-isr/Untitled%201.png)

#### ISR을 찾게된 배경

- 서비스의 API 속도가 느리고 현실적으로 속도 개선을 시킬수 없었다. → 페이지를 캐싱해야하는 방법이 필요
- 사용자가 똑같은 페이지를 여러명이 동시에 접근할 경우 서버의 부하가 늘어난다. → 캐싱된 페이지를 보여주는 경우에 디비 접근을 하지 않기 때문에 부하가 덜가게 된다.

#### 적용 후

적용 전에는 API 호출에 10~20초가 걸려서 페이지 뜨는 속도가 매우 느렸다. 적용 후에는 페이지가 즉각적으로 뜨고 이후 이미지 로드 시간만 더 소요된다.

![Untitled](/posts/nextjs-isr/Untitled%202.png)

[메인 페이지](https://popdeal.datepop.co.kr) 첫 컨텐츠가 이미지 슬라이드고 클릭하면 기획전으로 가는데 여기 페이지도 정보들이 많아 오래걸리는데 ISR 설정을 하고 매우 빨라졌다.

![Untitled](/posts/nextjs-isr/Untitled%203.png)

[홈 화면](https://popdeal.datepop.co.kr)에서 사용자가 들어갈수 있는 카테고리 항목이 19개인데 각각 페이지들을 미리 빌드하면 새로운 사용자가 '카페'를 누르면 빌드한 페이지를 바로 보여줄 수가 있다.

#### 주의할 점

- **개발환경**에서는 확인 불가능 !
build 하고 실행해야 적용된 페이지들이 보이기 때문에 반드시 `next build` 이후에 `next start` 해줄것. 개발환경인 `next dev`인 경우에는 적용되지 않는다.
- 그래도 **캐시**는 **이전 정보**
실시간 정보를 보여줘야하는 경우에는 적용하지 않거나 주기를 매우 짧게 가져간다. 반대로 하루에 한번의 업데이트만 있는 페이지들이 실제로 많은데 이 경우는 주기를 매우 길게 가져가도록 설정하면 된다.

### 마무리

리액트로 서버사이드 렌더링을 하는 Next.js와 거기서 최근에 출시한 기능인 ISR도 도입을 해봤다. 적용해보니 결과물이 빠르고 만족스러워 컨텐츠를 보여주는 서비스라면 반드시 도입을 고려해봐야한다. 소셜커머스와 웹툰 등이 대표적인 서비스일것 같다. 이런 서비스의 경우는 채용공고에서도 Next.js나 다른 서버사이드 렌더링 기술스택을 요구 할 수 밖에 없다. 앞으로 계속 개발하며 발전해나가면 데이트팝 프론트엔드 개발자 모집할 때 React에 이어 Next.js가 필수 기술일수도 있다.

#### 참고한 사이트

- [nextjs 시작하기](https://nextjs.org/docs/getting-started)
- [공식 nextjs 페이지 data fetching ISR](https://nextjs.org/docs/basic-features/data-fetching)

#### Next.js와 ISR 적용 결과물 사이트

- [https://datepop.co.kr](https://datepop.co.kr)
