---
layout: post
title: "데이트팝에 Motion Layout 적용하기"
author: ZeroDeg
categories: [Android, Datepop, MotionLayout]
image: assets/images/posts/android-motion-layout/Untitled.png
featured: true
---

## 데이트팝의 **첫인상을 바꿔보자** 😎

이번 프로젝트는 사내에서 ‘첫인상’이라는 이름으로 불리며 ‘첫인상’이라는 이름답게 데이트팝 앱 사용자에게 가장 먼저 보여지는 홈 화면을 개편하는 프로젝트였습니다. 주 목적은 기존의 트랜디하지 못한 UI와 디자인을 탈피하여 사용자로 하여금 새로움과 신선함을 선사하고자 함이었는데요. 이를 위해 저희는 네비게이션 형식의 애니메이션을 활용하여 화면에 꽉차는 지도와 큼지막한 사진과 영상이 있는 리스트를 동시에 보여줄 수 있는 형태를 생각하게 되었습니다.

![Untitled](/assets/images/posts/android-motion-layout/Untitled.png)

## 네비게이션 형식의 애니메이션… 그게 뭔데?

지금 이야기하고 있는 네비게이션 형식의 애니메이션은 손가락으로 스크린을 가볍게 슥- 올려주면 아래에 있던 화면이 자연스럽게 따라오는 형태의 애니메이션을 말하는데요. 손가락으로 터치를 할 때만 뷰가 움직이는 것이 아니라, 마치 스프링처럼 튕겨올라가는 듯한 느낌을 줘야 했기에 구현할 방법이 쉽게 떠오르지 않았습니다. 하지만 비슷한 애니메이션을 사용하는 곳이 이미 여럿 있었고 분명히 방법은 있다는 생각으로 자료를 찾아보기 시작했습니다.

![spring-release.gif](/assets/images/posts/android-motion-layout/spring-release.gif)

### 스프링 애니메이션 (Spring Animation)

위에 보이는 애니메이션은 안드로이드에서 제공하는 스프링 애니메이션 클래스를 활용한 예제입니다. 튕겨 올라간다는 듯한 느낌의 애니메이션을 찾다보니 친절한 구글이 Spring Animation 에 대한 문서를 보여주었는데요. 하지만 애니메이션 끝에서 튕겨 올라가는 것이 아니라 화면이 전환되는 애니메이션으로 이어져야 했기에 활용하기는 조금 어려워보였습니다.

<div style="display: flex; margin-bottom: 35px;">
<video controls width="50%">
    <source src="/assets/images/posts/android-motion-layout/anim_page_transformer_zoomout.mp4" type="video/mp4">
</video>

<video controls width="50%">
    <source src="/assets/images/posts/android-motion-layout/anim_page_transformer_zoomout%201.mp4" type="video/mp4">
</video>
</div>

### 뷰페이저 (ViewPager2)

사실 화면 전환이라는 말을 듣자마자 제일 먼저 생각났던 것은 ViewPager였습니다. 이미 데이트팝 앱 메인에서 오랫동안 사용하고 있었고 화면 전환에 최적화되어있는 만큼 충분히 활용성이 있어보였기 때문입니다. 하지만 이미 ViewPager를 사용하고 있는 상황에서 Fragment in ViewPager in Fragment in ViewPager in … 와 같은 구조를 만드는 것은 오히려 복잡도를 증가시키고 유지보수를 어렵게 할 것이라는 생각이 들었습니다. 이러한 이유로 결국 ViewPager를 활용하는 방법도 선택되지 못했습니다.

<div style="display: flex;margin-bottom:35px;">
    <img width="50%" src="/assets/images/posts/android-motion-layout/youtube-motion.gif" />
    <img width="50%" src="/assets/images/posts/android-motion-layout/fragment-transition-1.gif" />
</div>

### 모션 레이아웃 (Motion Layout)

마지막으로 찾은 방법은 모션 레이아웃 (Motion Layout) 이었습니다. 구글 문서에는 모션 레이아웃에 대해 “모션과 위젯 애니메이션 관리를 도와주는 레이아웃” 이라고 정의하고 있는데요. 레이아웃과 위젯간의 애니메이션을 자연스럽게 연출해주는 기능을 담당하고 있습니다. 예를 들어 애니메이션 시작 지점과 마지막 지점의 뷰를 정의해놓으면 모션 레이아웃은 시작과 끝을 자연스럽게 이어줍니다. 물론 여기에는 드래그 방향이나 가속도와 같은 옵션을 설정해주어야 하지만 직접 커스텀하여 만드는 것보다 훨씬 안정적인 성능을 보여줍니다. 거기다 스프링 애니메이션과 비슷한 옵션을 기본적으로 제공하며 뷰페이저에서 우려되었던 복잡성을 한 번에 해결해주었기 때문에 데이트팝 앱에 모션 레이아웃을 적용해봐도 좋겠다는 결론에 이르게 되었습니다.

모션 레이아웃을 사용하면 단 한 줄의 코드 없이도 손 쉽게 애니메이션을 구현할 수 있는데요. Transition과 ConstraintSet이 정의되어있는 Scene 파일과 Layout 안에 MotionLayout이 정의되어 있으면 곧바로 애니메이션을 재생할 수 있기 때문입니다. 아래는 간단한 예제 코드인데 이러한 코드만으로도 간단한 애니메이션을 구현할 수 있습니다. 아래는 간단한 예제 코드입니다.

**Scene 예제**

```xml
<?xml version="1.0" encoding="utf-8"?>
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:motion="http://schemas.android.com/apk/res-auto">

    <Transition
        android:id="@+id/singleScrollAnim"
        motion:constraintSetStart="@id/start"
        motion:constraintSetEnd="@+id/end"
        motion:duration="300"
        motion:motionInterpolator="linear"
        motion:transitionDisable="true">

        <OnSwipe
            motion:moveWhenScrollAtTop="true"
            motion:autoCompleteMode="continuousVelocity"
            motion:nestedScrollFlags="supportScrollUp"
            motion:touchAnchorId="@id/scrollView"/>

        <OnClick
            motion:targetId="@+id/swipeArrow"
            motion:autoCompleteMode="continuousVelocity"
            motion:nestedScrollFlags="supportScrollUp"
            motion:touchAnchorId="@id/scrollView"
            motion:touchAnchorSide="bottom" />

    </Transition>

    <ConstraintSet android:id="@+id/start">

        <Constraint
            android:id="@+id/locationAppBar"
            android:layout_height="56dp"
            motion:layout_constraintEnd_toEndOf="parent"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintBottom_toTopOf="parent"/>

        <Constraint
            android:id="@+id/scrollView"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            motion:layout_constraintBottom_toBottomOf="parent"
            motion:layout_constraintEnd_toEndOf="parent"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintTop_toTopOf="parent" />

        <Constraint
            android:id="@+id/conHelloDatepop"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintEnd_toEndOf="parent"
            motion:layout_constraintBottom_toBottomOf="parent"
            motion:layout_constraintTop_toBottomOf="@id/scrollView" />

    </ConstraintSet>

    <ConstraintSet android:id="@+id/end">

        <Constraint
            android:id="@+id/locationAppBar"
            android:layout_height="56dp"
            motion:layout_constraintEnd_toEndOf="parent"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintTop_toTopOf="parent"/>

        <Constraint
            android:id="@+id/scrollView"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            motion:layout_constraintBottom_toTopOf="parent"
            motion:layout_constraintEnd_toEndOf="parent"
            motion:layout_constraintStart_toStartOf="parent" />

        <Constraint
            android:id="@+id/conHelloDatepop"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            motion:layout_constraintEnd_toEndOf="parent"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintBottom_toBottomOf="parent"
            motion:layout_constraintTop_toTopOf="parent"/>

    </ConstraintSet>
</MotionScene>
```

**Motion Layout 예제**

```xml
<androidx.constraintlayout.motion.widget.MotionLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/parent"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layoutDescription="@xml/fragment_main_scene"
    tools:context=".ui.main.PlaceholderFragment">
.
.
.
</androidx.constraintlayout.motion.widget.MotionLayout>
```

기본적으로 Transition에서 애니메이션 지속 시간과 Interpolator 설정을 하게 됩니다. 그 뒤에 onSwipe와 onClick 을 통해 스크롤 했을 때와 특정 버튼을 눌렀을 때 어떤 애니메이션으로 동작할지 설정하면 되는데요. 여기서 속도, 최대속도, 가속도, 민감도 등 다양한 옵션을 추가적으로 설정할 수 있습니다.

마지막으로 ConstraintSet을 지정해줍니다. 여기서는 시작 지점의 UI와 마지막 지점의 UI를 설정해주면 되는데, 이렇게 두 개의 ConstraintSet을 만들어준 뒤에 Transition에서 Start와 End를 설정해주면 모든 게 끝이 납니다.

<div style="display: flex;margin-bottom:35px;">
    <img width="50%" src="/assets/images/posts/android-motion-layout/Nov-15-2022_20-46-48.gif" />
    <img width="50%" src="/assets/images/posts/android-motion-layout/MotionLayout.gif" />
</div>

애니메이션이 간단히 구현되었습니다. 실제로 구현할 때는 앞서 이야기한 속도나 가속도, 민감도 등 다양한 설정들을 적용시켜 준 뒤에, TransitionListener를 통해 애니메이션 상태에 따른 추가적인 로직이 추가되었습니다.

모션 레이아웃의 가장 큰 장점은 애니메이션과 관련된 기능을 앞서 예제 코드에서 살펴 본 것처럼 XML 파일로 분리하여 관리할 수 있으며 **재사용**할 수 있다는 점입니다. 만약 위에서 만들어놓은 애니메이션을 다른 Motion Layout 에서 사용하고자 한다면 별다른 수정 없이 곧바로 적용해서 사용할 수 있습니다.

앞으로 데이트팝 안드로이드 앱에서는 모션 레이아웃을 적극 활용할 예정입니다. 기존에 만들어둔 애니메이션을 재사용할 수 있기 때문에 새로 개발할 필요가 없기 때문입니다. 쉽고 빠르게 구현할 수 있으며 강력한 기능을 제공하는 모션 레이아웃과 함께 데이트팝 앱이 더 트랜디하고 역동적이 앱이 되었으면 하는 바람입니다.
