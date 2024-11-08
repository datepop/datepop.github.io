---
layout: post
title: "주니어 개발자의 프로젝트 참가기 (3) - Sprint 1차 달리기"
author: Coldzero
categories: [프로젝트, 킥오프, 개발자]
image: assets/images/posts/junior-developer-kick-off-3/junior-3-0.png
featured: false
---

안녕하세요 백엔드 개발자 2찬영의 프로젝트 참가기 3탄입니다.

스프린트 1차를 위해서 달린 후기를 적어보았습니다.

그럼 지금까지 어떻게 프로젝트를 준비해왔는지 알아볼까요~?😜

만약 저번일이 기억이 안난다면 다시보고 오기

- [1편 링크 🙄](https://blog.datepop.co.kr/junior-developer-kick-off/)
- [2편 링크 😘](https://blog.datepop.co.kr/junior-developer-kick-off-2/)

## Q1. 스프린트 1차 에서는 무엇을 했나요?

---

- 안드로이드에서 사용할 Mock API 작성
- 데이트팝 맵과 관련된 API를 개발했습니다.

→ 다음 질문을 통해서 좀더 스프린트 1차에서 관련된 작업을 자세하게 설명해보도록 하겠습니다.

## Q2. Mock API란?

---

mock api는 제가 만들 api에 관해서 예시로 갈 정보를 미리 만들어 보는 겁니다.

백엔드 차원에서 보낼 정보를 미리 json 파일을 만들어서 제공합니다.

저는 json 파일 형식의 기본을 잘 갖추지 못해서 안드로이드 개발자 분이 고생하셨습니다 😭

만든 json 파일을 통해서 mocky를 통해서 mock api를 작성하였습니다.

“**The world easiest & fastest tool belts to mock your APIs”**

→ 위와 같은 슬로건을 가지고 사이틑 운영하는 [mocky](https://designer.mocky.io/) 에서 쉽고 빠르게 만들수 있었습니다.

## Q3. 데이트팝 맵과 관련된 API는 어떤것들이 있나요?

---

먼저 데이트팝 맵에 대해서 이야기하기 전에 기존의 앱이 어떻게 이뤄져 있는지를 알 필요가 있습니다.

![Untitled](/assets/images/posts/junior-developer-kick-off-3/junior-3-1.png)

기존의 맵은 이처럼 생겼습니다. 이 맵의 단점은 서버에서 불러오는것이 아닌 앱 자체내에 하드코딩 되어 있다는 점이었습니다.

하드코딩은 서버에서 정보를 가져오는것이 아닌 앱 자체내에 저장되어있어서 불편한점이 많습니다.

단점으로는

1. 지역을 업데이트하기가 매우 어렵다(앱 내의 정보를 직접 바꿔서 배포해야 되기때문)
2. 디자인을 바꿀려고 하면 대규모 작업이 발생하게 된다.
3. 서비스 제공자가 자체적으로 커스텀하기에도 어려움이 존재한다.

이와 같은 단점으로 데이트팝 맵의 기존단점을 고칠수 있는 API 개발을 했습니다.

- 지도(Map Item) 관련 API
- 지도(Map Item)와 지역(Map Info)을 연결해주는 관련 API
- 기존에 존재했던 지역 분류를 DB화 시키는 작업

기존에 문제되고 어려웠던 부분은 사라지고 좀더 업로드하는 사람도 사용자도 즐길 수 있는 앱으로 변화 하였습니다.

## Q4. 스프린트 1차를 겪으며 어려웠던 점

---

가장 어려운것은 다가오는 납기일을 맞이하면서 현실과 마주하는 점이었습니다.

개발 속도가 빨리 나오지 못하는 상황속에서 이해하고 처리해야 하는 업무의 난이도는 어려웠습니다.

결정적으로 ORM과 query의 이해도가 떨어지는 현실에 매우 자존감도 낮아지는 1차였던것 같습니다.

그래도 안드로이드 개발자의 배려와 팀장님의 도움으로 무사히(?) 1차스프린트를 마감하였습니다.

## 글을 마치며…

---

회사에서 처음 진행하는 프로젝트인 만큼 열정을 쏟으며 진행하고 있습니다.

매번 느끼지만 절대 혼자서 할 수 있는 일은 없고 협력이 필수 인것 같습니다.

다음 2차 스프린트에서는 좀 더 성장한 모습을 보여 드리며 글을 마치고 싶습니다.

저번 글에서

_`끝내기 위해서 노력하고 또한 어쩌면 야근하는 날이 더 많을수도 있고 개발을 빨리 해내지 못하는 제 자신에게 화날수도 있을 수도 있을것입니다.`_

이렇게 문장을 썼는데 예측한데로 맞이 하였습니다.

제 자신의 부족한 부분을 많이 느끼고 어려움도 느끼는 날이 더 많았습니다.

![Untitled](/assets/images/posts/junior-developer-kick-off-3/junior-3-2.png)

위의 짤처럼 어지럽고 일이 안잡힐때도 있었지만 그건 제 자신의 부족함이 주는 병이었습니다.

제 자신의 부족함을 느끼고 더 발전 하기 위해 노려하면서 마치게된 1차 스프린트였습니다.

2차 스프린트에는 변화된 모습과 프로젝트의 결과물을 들고 오겠습니다.😜

![Untitled](/assets/images/posts/junior-developer-kick-off-3/junior-3-3.png)

모두들 안녕~ 🥹 🤩  🙌🏻

<div style="max-width: 500px; width: 100%; margin-top: 40px;" >
<a href="https://datepop.co.kr/exhibition/list/666" target="_blank" rel="noopener noreferrer">
 <img src="https://cdn.datepop.co.kr/image/marketing/exhibition/list/2024-10-31/tz9faq9gl8.png=w1080">
 </a>
</div>