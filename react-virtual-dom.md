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





## Reference

* [React Virtual DOM Explained in Simple English](https://programmingwithmosh.com/react/react-virtual-dom-explained/)
* [Reconciliation](https://reactjs.org/docs/reconciliation.html#gatsby-focus-wrapper)