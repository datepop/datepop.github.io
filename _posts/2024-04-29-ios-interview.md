---
layout: post
title: "데이트팝 iOS 주니어 개발자 인터뷰 후기"
author: Springkjw
categories: [iOS, 채용, Swift, Datepop]
image: assets/images/coding-tests-for-interviews.jpg
featured: true
---

## 데이트팝 iOS 주니어 개발자 인터뷰 후기

안녕하세요. 여러분에게 사랑을 전하는 데이트팝의 CTO, 권재원입니다.
최근 데이트팝에서 진행한 iOS 주니어 개발자 채용 과정을 진행하면서 경험한 기술 인터뷰의 후기를 공유하고자 합니다. 이 글이 iOS 개발자 취업을 준비하는 주니어 분들께 실질적인 도움이 되기를 바랍니다.

아래 인터뷰 질문은 데이트팝에서 채용을 위해 많은 고민을 하고 준비한 질문들입니다. 상업적 이용 이외에 학습을 위해 자유롭게 내용을 참고해주시고 공유해주시면 감사하겠습니다.

## iOS 개발자 채용 시 데이트팝이 바라는 개발자상

데이트팝에서는 iOS와 Swift에 대한 열정과 함께 탄탄한 기본기를 갖춘 주니어 개발자를 찾고 있습니다. 인터뷰는 주로 필수적으로 학습해야 하는 내용에 초점을 맞춰 진행되었으며, 이론을 단순히 외운 것이 아닌 깊이 이해하고 있는지를 평가하였습니다.

## 인터뷰 질문

### Swift의 Class와 Struct

Swift의 핵심 개념인 Class와 Struct의 차이를 이해하고 있는지 확인하기 위해 관련 질문을 포함하였습니다 각 타입에 따라 어느 메모리에 저장하고 사용하는지 정확하게 이애하고 있다면 쉽게 대답할 수 있는 질문으로 구성하였습니다.
Value 타입과 Reference 타입에 대한 정확한 이해가 필요합니다.

- `EmptyClass` 클래스를 이용하여 `HugeStruct`와 `HugeClass`을 선언하고 두 함수를 만들어 실행 시킬 때 실행시간 차이는 어떻게 날까요?

```swift
final class EmptyClass {}

struct HugeStruct {
    var emptyClass = EmptyClass()
    var emptyClass2 = EmptyClass()
    var emptyClass3 = EmptyClass()
    var emptyClass4 = EmptyClass()
    var emptyClass5 = EmptyClass()
    var emptyClass6 = EmptyClass()
    var emptyClass7 = EmptyClass()
    var emptyClass8 = EmptyClass()
    var emptyClass9 = EmptyClass()
    var emptyClass10 = EmptyClass()
}

class HugeClass {
    var emptyClass = EmptyClass()
    var emptyClass2 = EmptyClass()
    var emptyClass3 = EmptyClass()
    var emptyClass4 = EmptyClass()
    var emptyClass5 = EmptyClass()
    var emptyClass6 = EmptyClass()
    var emptyClass7 = EmptyClass()
    var emptyClass8 = EmptyClass()
    var emptyClass9 = EmptyClass()
    var emptyClass10 = EmptyClass()
}

func createBunchOfReferencesOfClass() {
    var array = [HugeClass]()
    let object = HugeClass()
    for _ in 0..<10000000000000 {
        array.append(object)
    }
}

func createBunchOfCopiesOfStruct() {
    var array = [HugeStruct]()
    let object = HugeStruct()
    for _ in 0..<10000000000000 {
        array.append(object)
    }
}
// 실행 시간이 둘 중에 어느게 더 길지?
createBunchOfReferencesOfClass()
createBunchOfCopiesOfStruct()
```

- `CFGetRetainCount`가 객체의 Reference Counting을 반환해주는 함수일 경우, 아래 코드를 실행 시키면 출력이 어떻게 될까요? 그 이유도 같이 설명해주세요.

```swift
final class EmptyClass {}

final class ClassOfClasses {
    let emptyClass = EmptyClass()
    let emptyClass2 = EmptyClass()
    let emptyClass3 = EmptyClass()
}

let classOfClasses = ClassOfClasses()
let reference = classOfClasses
let reference2 = classOfClasses
let reference3 = classOfClasses

// 아래 코드 출력이 어떻게 될지? 그 이유는?
CFGetRetainCount(classOfClasses)
CFGetRetainCount(classOfClasses.emptyClass)
CFGetRetainCount(classOfClasses.emptyClass2)
CFGetRetainCount(classOfClasses.emptyClass3)
```

- `CFGetRetainCount`가 객체의 Reference Counting을 반환해주는 함수일 경우, 아래 코드를 실행 시키면 출력이 어떻게 될까요? 그 이유도 같이 설명해주세요.

```swift
final class EmptyClass {}

struct StructOfClasses {
    let emptyClass = EmptyClass()
    let emptyClass2 = EmptyClass()
    let emptyClass3 = EmptyClass()
}

let structOfClasses = StructOfClasses()
let copy = structOfClasses
let copy2 = structOfClasses
let copy3 = structOfClasses

// 아래 코드 출력이 어떻게 될지? 그 이유는?
CFGetRetainCount(structOfClasses)
CFGetRetainCount(structOfClasses.emptyClass)
CFGetRetainCount(structOfClasses.emptyClass2)
CFGetRetainCount(structOfClasses.emptyClass3)
```

### Life Cycle 실험

UIKit에서 두 개의 ViewController이 구성있을 때 서로의 VC가 전환되는 여러 순간에 대해 어떻게 동작할지, View Life Cycle과 관련한 이해를 확인하는 질문으로 구성하였습니다.
iOS 개발자라면 무조건 해볼 Life Cycle 실험에 대해 다시 상기해보고 각 화면 전환에 대해 정확하게 개념을 이해하고 있으면 됩니다.

UIKit에서는 여러 Life Cycle 관련 메소드들을 제공하지만 이번엔 아래 5가지 메소드에 대해서만 다뤄볼 예정입니다. 각 메소드들에 대한 개념과 동작 방식에 대한 질문도 같이 이루어졌습니다.

```swift
ViewDidLoad
ViewWillAppear
ViewDidAppear
ViewWillDisappear
ViewDidDisappear
```

- 첫 화면이 보여지는 경우, VC에서 어떤 Life Cycle이 어떤 순서대로 호출이 될까요?

<div style="max-width: 300px;">
    <img src="https://cdn.datepop.co.kr/datepop/interview/ios-interview-lifecycle-1.gif" alt="iOS_인터뷰_질문_1" />
</div>

iOS에서는 화면전환 방식으로 Navigation 방식과 Modal 방식이 있습니다. 처음으로 Modal 방식에서 화면을 전체를 덮지 않을 경우를 생각해봅시다.

- 첫번쨰 화면에서 두번째 화면으로 전환하는 경우, 두 화면에서는 Life Cycle이 어떤 순서대로 호출이 될까요?
  이 때 첫번째 화면에서는 Life Cycle 메소드들이 호출이 되나요? 그 이유는 무엇인가요?

<div style="max-width: 300px;">
    <img src="https://cdn.datepop.co.kr/datepop/interview/ios-interview-lifecycle-2.gif" alt="iOS_인터뷰_질문_2" />
</div>

- 두번째 화면에서 사용자가 화면을 아래로 당겨 내리기 직전까지 끌어오는 순간에, 두 화면에서는 Life Cycle이 어떤 순서대로 호출이 될까요? (아직 두번째 화면이 내려오지 않은 상황) 그렇게 생각한 이유는 무엇일까요?

<div style="max-width: 300px;">
    <img src="https://cdn.datepop.co.kr/datepop/interview/ios-interview-lifecycle-3.gif" alt="iOS_인터뷰_질문_3" />
</div>

- 두번째 화면을 다 내리기 직전 사용자가 변덕이 생겨 두 번째 화면을 다시 덮어버렸을 때, 두 화면에서는 Life Cycle이 어떤 순서대로 호출이 될까요? 그렇게 생각한 이유는 무엇일까요?

<div style="max-width: 300px;">
    <img src="https://cdn.datepop.co.kr/datepop/interview/ios-interview-lifecycle-4.gif" alt="iOS_인터뷰_질문_4" />
</div>

- 두번째 화면을 다 내려 첫번째 화면으로 다시 돌아갈 경우, 두 화면에서는 Life Cycle이 어떤 순서대로 호출이 될까요? 그렇게 생각한 이유는 무엇일까요?

<div style="max-width: 300px;">
    <img src="https://cdn.datepop.co.kr/datepop/interview/ios-interview-lifecycle-5.gif" alt="iOS_인터뷰_질문_5" />
</div>

---

이번에는 Navigation 방식을 생각해봅시다. 첫번째 화면이 보여지고 나서 두번째 화면까지 보여진 다음에 다시 첫번째 화면으로 돌아가면 두 화면에서는 Life Cycle이 어떤 순서대로 호출이 될까요? 첫번째 화면의 `ViewWillAppear`가 먼저 호출이 될까요 아니면 두번째 화면의 `ViewWillDisappear`가 먼저 호출이 될까요? 두 화면의 메소드 호출 순서를 맞춰볼까요?

<div style="max-width: 300px;">
    <img src="https://cdn.datepop.co.kr/datepop/interview/ios-interview-lifecycle-6.gif" alt="iOS_인터뷰_질문_6" />
</div>

### 알고리즘 라이브코딩 테스트

기초적인 동적 프로그래밍 문제를 통해 알고리즘에 대한 기본기를 확인하였습니다.

```
두 개의 양의 정수 a와 b가 주어졌을 때, a와 b 사이의 범위(포함)에서 서로 다른 자릿수를 가진 숫자의 개수를 반환하세요.
```

## 기술 인터뷰를 마무리하며

데이트팝 서비스는 지속적으로 성장하며, 이번에 iOS 주니어 개발자를 채용할 수 있는 기회를 얻었습니다. 회사에서는 오랜만에 채용하게 되는 iOS 주니어 개발자이기에 기본기가 탄탄하신 분들을 모시고자 많은 고민을 하며 인터뷰 질문을 준비했습니다. 지원자들 중 많은 분들이 깊이 있는 학습을 해오셨고, 주니어임에도 불구하고 높은 수준의 역량을 보여주었습니다. 단기간에 학습한 내용이 실제 이해와 적용 가능한 지식으로 내재화되었는지 스스로 점검하는 기회가 되었으면 좋겠습니다. 감사합니다.
