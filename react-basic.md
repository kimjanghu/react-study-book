# Hello React

***해당 문서는 Code Squad `클린 리액트 웹 프로그래밍` 과정의 강의 자료를 기반으로 작성한 문서입니다.***



## 1. Background

---

### Why react?

* 과거에는 서버에서 페이지를 받아 인터렉션에만 집중
* 현재는 서버로부터 데이터를 받아 렌더링
* DOM 조작이 빈번하게 발생한다.
* DOM Tree가 복잡하기 때문에, 추가, 삭제 등 조작 방법에 따라 렌더링 속도가 차이난다.

-> React는 최적화된 DOM 조작을 한다.

* 웹 접근성이 향상
* 네트워크 속도 및 디바이스 성능 향상
* 단순히 콘텐츠를 제공하는 웹 서비스에서 인터랙티브한 웹 서비스로 발전
* 더 나은 UX가 필요해집

-> SPA의 등장





## 2. React

---

> `A JavaScript library for building user interfaces`

[React Official Website](https://reactjs.org/)

* Framework보다는 Library에 가깝다.
* fetch, routing, state management 등을 기본으로 제공하지 않는다.
* React는 View를 담당하며, 그 외 필요한 모듈은 추가해서 사용한다.
* 개발자가 View 작업에 좀 더 집중하게하며, 렌더링 성능과 동작은 알아서 최적화
* DOM 조작을 위해 template를 분리하지 않고, UI 컴포넌트 단위로 표현
* jsx 문법을 사용하여, 컴포넌트를 생성



### Virtual DOM

> `Virtual DOM은 DOM Object의 경량화된 복사 버전이다.`

[Explain Virtual DOM](https://www.codecademy.com/articles/react-virtual-dom)

* DOM Object와 같은 속성을 지니고 있지만, 실제 화면을 직접적으로 변화시키지는 못한다.
  * 따라서, real DOM 조작은 느리지만 Virtual DOM의 조작은 빠르다.

* React는 Virtual DOM 활용으로 DOM 조작을 효율적으로 한다.
* Virtual DOM과 DOM Tree를 비교하여 변경사항을 파악한다.
* 변경 부분만 수정하여 DOM 변경, Re-render 최소화
* 부모 컴포넌트가 변경되면 모든 자식들이 바뀐다.
* 최적화를 하려고하지만 변경사항이 약간이라도 있다면 적극적인 렌더링
* 따라서, 렌더링을 최소화하기 위한 최적화 작업이 필요하다.
  * `React.memo`, `useMemo`, `useCallback` 등 활용

![react-virtual-DOM](images/react-virtual-DOM.png)

<center>source: https://www.oreilly.com/library/view/learning-react-native/9781491929049/ch02.html</center>





## 3. React Components

---

* React는 컴포넌트 주도 개발 방식을 따른다.
* View를 의미적으로 구분하여, 독립적인 컴포넌트로 만든다.
* 계층을 이루며 UI 요소별로 여러개 컴포넌트 단위로 구성

```javascript
// component example
import "./styles.css";
import React, { useState, useEffect } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```





## 4. JSX

---

React에서 사용하는 표준 html template 문법

* `render()` 함수 안에 template 작성
* real DOM 생성을 위해 필요
* JSX 문법은 HTML을 코딩한다고 생각하면 편하다.
* JSX로 표현하면, DOM API를 사용하지 않고 DOM 변경 가능하다.
* JSX안에서 `{}` 로 JavaScript 코드 구현 가능
* JSX는 브라우저 상에서 해석되지 않으므로, webpack과 babel을 통해 transpiling을 해야한다.
* JS 파일로도 react를 작성가능하지만, JSX 확장자로 쓰였다면 webpack에서 View Component라고 파악 가능하다.
* 따라서 일단 JS 파일은 렌더링과 관련없는 utils 등의 함수에서 사용하고, Component는 JSX 파일로 작성한다.





## 5. React component hierarchy

---

* React는 HTML과 같은 계층적인 컴포넌트 개발이 필요하다.
* React는 Component를 분석해서 Node 객체로 저장한다.
  * JSX + Hooks 형태로 Component를 분석
* 계층 구조일 경우 데이터 교환과 컴포넌트의 호출은 `state와 props`로 이루어진다.
  1. state 변경
  2. 레이아웃 조정(reflow), 페인팅 조정(repaint) 등 re-render
     * reflow, repaint -> re-render



### State: Component 내부 상태

---

* 컴포넌트마다 각각의 데이터(State)를 가질 수 있다.
* 컴포넌트가 가지고 있는 값이며, 변경이 되는 대상을 `state`라고 표현
* 각 state는 useState 메서드로 관리한다.

### Props: 상위 컴포넌트로부터 전달 받은 데이터 Object

---

* 상위 컴포넌트로부터 받은 state는 props 객체를 통해 접근 가능하다.
* props 객체는 read-only로 수정하지 않아야한다.



### Rendering flow

---

* 자식 컴포넌트는 부모 컴포넌트로부터 받은 props에 따라 렌더링 방식이 결정된다.
  * 즉, 자신이 가지고 있는 state 값을 제외하고 대부분은 부모에 있는 state에 따라 렌더링된다.

1. state 변경
2. state를 사용하고 있는 JSX 감지 후 변경
3. Virtual DOM과 Real DOM을 비교해서 렌더링 준비
4. 등록한 Hooks function 실행

* 개발자가 하는 것은 state 변경, Hooks function 조작
* 렌더링 이후 useEffect 실행
* 컴포넌트를 그대로 실행하는 것이아닌 재해석 후 다른 구조(Node Object)로 저장





## 6. Event

---

* inline방식으로, onClick과 같은 예약어로 event를 추가한다.
* 부모 컴포넌트는 자식컴포넌트에서 eventHandler를 전달할 수 있다.

```js
<button onClick={() => setCount(count + 1)}>Click me</button>
```

```js
// 데이터 변경 처리 실습
import React, { useState } from "react";
import ReactDOM from "react-dom";

function Child(props) {
  return (
    <div>
      <input onChange={props.inputHandler} />
    </div>
  );
}

function Show(props) {
  return (
  	<div>
    	{props.content}
    </div>
  )
}

function Parent() {
  const [content, setContent] = useState("");

  const inputHandler = e => {
    setContent(e.target.value);
  };

  return (
    <div>
      <h1>안녕하세요</h1>
      <Child inputHandler={inputHandler} />
			<Show content={content} />
    </div>
  );
}

ReactDOM.render(<Parent />, document.querySelector("#root"));
```





## 7. map 등 Array Methods를 활용해 반복코드 줄이기

---

```js
function Member() {
  const members = ["yagom", "crong", "jk", "honux!"];
  const membersList = members.map((v, i) => <li key={i}>{v}</li>);
  return <ul>{membersList}</ul>;
}
```

* 반복적인 list element는 렌더링 최적화를 위해 `key` 속성 필요







## Hooks(추후 Hooks 문서로 옮김.)

[Rules of Hooks](https://reactjs.org/docs/hooks-rules.html)

### 반복문, 조건문, 중첩된 함수 내에서 Hooks를 조작 및 호출하면 안된다.

* 만약 조건문에서 Hooks를 실행할 때, false 조건일 경우 해당 Hooks를 건너뛰고 다음 Hooks를 실행시키기 때문에, 순서를 보장할 수 없다.
* Hooks를 React 함수의 최상위에서 실행해야한다.
  * 각 컴포넌트가 렌더링될 때마다 항상 동일한 순서로 Hooks(useState, useEffect)가 호출되는 것을 보장해준다.

### 일반적인 JavaScript 함수 내에서 Hooks를 실행시키면 안된다.

* React 함수 컴포넌트에서 Hooks를 호출한다.
* Custom Hooks에서 Hooks를 호출한다.

-> 해당 규칙을 따른다면 컴포넌트 내에서 모든 state 관련 로직을 소스코드에서 명확하게 보이게할 수 있다.

유즈이팩트

렌더링 다음 실행된다.

빈배열을 넣어야하는이유

유즈이펙트 함수는 항상 실행될 필요가 없다.

디펜던씨, 의존되는게 없으면 필요할 때만 실행한다.

만약 있다면 항상 재렌더링된다.

상태가 바뀌면 다시 다 렌더링된다.

하지만 변경되는 부분이 없으면 굳이 제 렌더링될 필요없다.

마지막에만 어팬드차일드같은 방법으로 노드만 추가해준다.



함수를 사용하는 이유? 이전 상태값을 기억했다가 활용해야한다. => 클로저 개념



캐시 등을 기억하고 싶을 때 useRef를 사용

렌더링에 영향에 주지않는 어떤 상태값을 유지하고 싶을때







