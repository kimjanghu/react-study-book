# React - Virtual DOM

## 1. Real DOM

---

> `DOM, Document Object Model`

* DOM은 어플리케이션 UI를 표현한다.
* 어플리케이션의 UI 상태에 변화가 생기면 DOM은 업데이트 된다.
* 따라서 DOM 조작이 빈번하다면 성능에 영향을 줘 어플리케이션을 느리게 만든다.



### DOM 조작이 느린 이유

---

* DOM은 Tree 구조로 표현된다.
  * 따라서 DOM에 대한 변경 및 업데이트가 빠르다.
* 하지만, 변경 이후에 업데이트된 UI를 적용하기 위해서는 element와 그 child element는 re-render 되어야한다.
* re-render 또는 re-painting 작업은 느리다.
* 그러므로 모든 DOM이 re-render되어야하기 때문에, 결과적으로 UI 컴포넌트가 많을 수록 DOM 업데이트 비용이 비싸진다.





## 2. Virtual DOM

---

> `Virtual DOM은 DOM Object의 경량화된 복사 버전이다.`

* 어플리케이션 UI가 변화하면 Virtual DOM은 Real DOM을 대신해 업데이트 된다.



### Virtual DOM이 빠른 이유

---

* Virtual DOM은 Tree 구조로 표현된다.
* 모든 element는 Tree의 Node이다.
* element에 변화가 생기면, 새로운 Virtual DOM Tree가 생긴다.
* 새로운 Virtual DOM Tree는 이전의 것과 차이를 비교한다.
* 해당 과정이 끝나면 Virtual DOM은 Real DOM을 변경할 수 있는 최적의 방법을 계산하며 최소한의 Real DOM 조작을 보장한다.
* 따라서, Real DOM이 업데이트 되는 것보다 소모되는 비용을 줄일 수 있다.

![react-virtual-DOM](images/react-virtual-DOM.png)

<center>source: https://www.oreilly.com/library/view/learning-react-native/9781491929049/ch02.html</center>





## 3. How does React use Virtual DOM

---

* React의 모든 UI Component들은 각각의 상태를 가지고 있다.
* React는 Observable 패턴을 따르며 상태의 변화를 감지한다.
* 따라서 React는 Component의 상태가 변경될 때, Virtual DOM Tree를 업데이트한다.
* Virtual DOM이 업데이트되면, 이전의 Virtual DOM과 비교한다. (diffing)
* 변경된 Virtual DOM의 객체를 알면, Real DOM에서 해당 객체만 업데이트한다.
* 이러한 과정은 추상되어있기 때문에, 실제 개발자가 관여하지 않아도된다.
* 개발자는 Component의 상태를 변경하기만 하면 되며, 나머지는 React에서 처리한다.
* 결과적으로
  * Component의 state 혹은 props가 업데이트 될 때마다, 변경을 감지하여 Compoent를 re-render한다.
  * 이 때, Virtual DOM을 업데이트하고, 변경된 객체만 Real DOM에 반영한다.



### Batch Update

* React는 Real DOM 업데이트 시 Batch Update 매카니즘을 따른다.
* 모든 작은 상태의 변화가 있을 때마다 업데이트를 보내는 대신 Real DOM에 대한 업데이트만 일괄적으로 보낸다.
* UI re-painting 작업은 비용이 많이 들며, React는 효율적으로 Real DOM이 Batch된 업데이트만 UI에서 re-painting하도록 보장한다.



## Reference

* [React Virtual DOM Explained in Simple English](https://programmingwithmosh.com/react/react-virtual-dom-explained/)
* [Reconciliation](https://reactjs.org/docs/reconciliation.html#gatsby-focus-wrapper)