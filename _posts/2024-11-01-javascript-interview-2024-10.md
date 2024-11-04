---
layout: post
title: "데이트팝 프론트엔드 개발자 기술면접"
author: Springkjw
categories: [Javascript, 채용, Datepop]
image: assets/images/coding-tests-for-interviews.jpg
featured: true
---

## 데이트팝  주니어 개발자 기술면접

이번 시간에는 React useState Hook을 직접 만들어가면서 Hook이 어떻게 동작하는지 확인해보는 시간을 가져보겠습니다.

### React useState 흉내내기


React `useState`를 흉내내기 위해서 `useState`라는 함수를 직접 작성해보았습니다. 상태값 `foo` 와 상태값을 변경하는 `setFoo` 함수가 의도하는대로 동작하는지 설명해주세요. 그렇지 않다면 어떻게 코드를 수정하면 될지 제시해주세요.

```javascript
function useState(initialValue) {
  var _value = initialValue;

  function setState(newValue) {
    _value = newValue;
  }

  return [_value, setState];
}

const [foo, setFoo] = useState(0);
console.log(foo); // 여기에서는 초기값인 0이 출력됩니다.
setFoo(1);
console.log(foo); // 여기에서 상태값이 1로 변경이 될까요?
```

### 본격적으로 React useState 뿌시기

위에서 React `useState` 뼈대를 만들어보면서 공부해보았다면, 이번엔 실제 React 모듈을 만들어 React 내에서 `useState`가 어떻게 동작하는지 배워가보겠습니다. 

`DatepopReact`라는 즉시 실행함수를 만들어 보았습니다. `useState`와 `render`가 있어 `DatepopComponent`를 실행 시켜주고 `render` 함수를 실행시키게 됩니다.

`DatepopComponent`에서는 `DatepopReact`의 `useState`를 이용하여 `count`와 `text` 상태값을 관리하도록 해보았습니다. `click` 함수와 `type` 함수를 실행 시킬 경우, 의도대로 상태값이 변경되는지 확인해보십다.

```javascript
const DatepopReact = (function () {
  let _value;

  function useState(initialValue) {
    let state = _value || initialValue;

    const setState = function (newValue) {
      _value = newValue;
    };

    return [state, setState];
  }

  function render(Component) {
    const C = Component();
    C.render();
    return C;
  }

  return {useState, render};
})();

function DatepopComponent() {
  const [count, setCount] = DatepopReact.useState(0);
  const [text, setText] = DatepopReact.useState("popdeal");

  return {
    render: function () {
      console.log("render: ", {count, text});
    },
    click: function () {
      setCount(count + 1);
    },
    type: function (value) {
      setText(value);
    },
  };
}

let App = DatepopReact.render(DatepopComponent);
// 이 때, 출력으로 render: {count: 0, text: "popdeal"} 이 출력됩니다.
App.click();
App = DatepopReact.render(DatepopComponent);
// 이 때, 출력값으로 무엇이 나올까요?
App.type("poppass");
App = DatepopReact.render(DatepopComponent);
// 이 때, 출력값으로 무엇이 나올까요?
```

### React useState 제대로 만들어보기

위의 문제에서 의도대로 상태값이 변경되지 않는다는 것을 알 수 있습니다. 이는 `DatepopReact`가 `count`와 `text`의 값을 독립적으로 관리하지 못하기 때문에 발생하는 문제입니다. 실제 `React`에서는 상태값을 컴포넌트 내에서 순서대로 관리합니다. 이를 위해 아래와 같이 코드를 수정하면 `setState`만 완성하면 됩니다. 이제 각 상태의 인덱스를 고정해 클로저 문제를 해결해보세요! 어떻게 완성시키면 될까요?

```javascript
const DatepopReact = (function () {
  let states = [];
  let index = 0;

  function useState(initialValue) {
    let state = states[index] || initialValue;

    const setState = function (newValue) {
      // 코드가 동작할 수 있도록 코드를 작성해주세요!
    };

    return [state, setState];
  }

  index += 1;

  function render(Component) {
    index = 0;
    const C = Component();
    C.render();
    return C;
  }

  return {useState, render};
})();

function DatepopComponent() {
  const [count, setCount] = DatepopReact.useState(0);
  const [text, setText] = DatepopReact.useState("popdeal");

  return {
    render: function () {
      console.log("render: ", {count, text});
    },
    click: function () {
      setCount(count + 1);
    },
    type: function (value) {
      setText(value);
    },
  };
}

let App = DatepopReact.render(DatepopComponent);
// 이 때, 출력으로 render: {count: 0, text: "popdeal"} 이 출력됩니다.
App.click();
App = DatepopReact.render(DatepopComponent);
// 이 때, 예상 출력값은 render: {count: 1, text: "popdeal"}
App.type("poppass");
App = DatepopReact.render(DatepopComponent);
// 이 때, 예상 출력값은 render: {count: 1, text: "poppass"}
```

### React useState 완성하기

이제 위에서 React useState가 의도대로 동작하는 것을 알 수 있습니다. 하지만, 하나가 아쉽네요.

```javascript
    click: function () {
      setCount(count + 1);
    },
```

click 함수를 보면 count 자기 자신을 하나 증가 시키는 구조로 되어 있는데 React에서는 상태값이 비동기로 변경되기 때문에 이전 상태값을 매개변수로 받아 변경하는 방식을 사용합니다.

```javascript
    click: function (prev) {
      setCount(prev + 1);
    },
```

이런 식으로 변경이 가능합니다. 이렇게 되면 `useState` 도 같이 변경해야합니다.

```javascript
const DatepopReact = (function () {
  let states = [];
  let index = 0;

  function useState(initialValue) {
    let state = states[index] || initialValue;
    const _index = index;

    const setState = function (newValue) {
      if (typeof newValue === "function") {
        states[_index] = newValue(...); // 이전 상태값으로 인자를 어떻게 넣어주면 될까요?
      } else {
        states[_index] = newValue;
      }
    };

    return [state, setState];
  }

  index += 1;

  function render(Component) {
    index = 0;
    const C = Component();
    C.render();
    return C;
  }

  return {useState, render};
})();

function DatepopComponent() {
  const [count, setCount] = DatepopReact.useState(0);
  const [text, setText] = DatepopReact.useState("popdeal");

  return {
    render: function () {
      console.log("render: ", {count, text});
    },
    click: function () {
      setCount(function (prev) {
        return prev + 1;
      });
    },
    type: function (value) {
      setText(value);
    },
  };
}

let App = DatepopReact.render(DatepopComponent);
// 이 때, 출력으로 render: {count: 0, text: "popdeal"} 이 출력됩니다.
App.click();
App = DatepopReact.render(DatepopComponent);
// 이 때, 예상 출력값은 render: {count: 1, text: "popdeal"}
App.type("poppass");
App = DatepopReact.render(DatepopComponent);
// 이 때, 예상 출력값은 render: {count: 1, text: "poppass"}
```

<div style="max-width: 500px; width: 100%; margin-top: 40px;" >
<a href="https://datepop.co.kr/exhibition/list/666" target="_blank" rel="noopener noreferrer">
 <img src="https://cdn.datepop.co.kr/image/marketing/exhibition/list/2024-10-31/tz9faq9gl8.png=w1080">
 </a>
</div>