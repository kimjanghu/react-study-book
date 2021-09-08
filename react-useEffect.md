# react-useEffect

> useEffect? Class 컴포넌트의 componentDidMount LifeCycle에서 처럼 side effects 작업을 진행할 수 있다.

* 인자로 전달되는 함수는 렌더링이 모두 완료(DOM 업데이트 완료)된 이후에 수행된다.
* 컴포넌트의 state에 접근하기 위해서는 `useEffect` 가 컴포넌트 함수안에 있어야한다.
* `useEffect` 는 비동기적인 방식
  * 즉, 화면의 업데이트를 방해하지 않는다.
  * `useLayoutEffect` 는 동기적인 방식
* `useEffect` 메서드는 두 번째 인자로 준 value가 변경될 때만 동작
  * 렌더링 이후 한번만 동작시키고 싶다면, `[]` 빈 배열 전달
* `useEffect` 와 `useState` 는 여러개 선언이 가능하며, 리액트는 순서대로 실행시킨다.
  * 따라서, 관련있는 effect 함수들만 묶어서 생성하는 것이 가능하다.

```js
componentDidMount() {
    fetch("/api/user").then(
        res => this.setState({...this.state, user: res.user})
    )
}

//hook
useEffect(() => {
    fetch("/api/user").then(
        res => setState(res.user)
    )
})
```

```js
useEffect(() => {
    function getFetchUrl() {
        return '/search?query=' + query;
    }

    async function fetchData() {
        const result = await axios(getFetchUrl());
        setData(result.data);
    }
    fetchData();
}, [query]);
```

* 위 예제에서 query가 변경될 때만 `useEffect` 메서드 실행



## Cleaning up an effect

---

* effects는 컴포넌트가 화면에서 제거되기 전에, 정리해야하는 리소스를 만든다.
  * 예를들어, 구독이나 Timer의 경우
* useEffect에서 정리해야할 리소스를 return한다.

```js
useEffect(() => {
  const subscription = props.source.subscribe();
  return () => {
    // Clean up the subscription
    subscription.unsubscribe();
  };
});
```

* 메모리 누수를 방지하기 위해 Clean-up 함수는 화면에서 컴포넌트가 제거되기 전에 실행된다.
* 만약 컴포넌트가 여러번 반복해서 실행된다면, 다음 effect가 실행되기 전에 이전 effect가 정리된다.



## Timing of effects

---

* componentDidMount와 componentDidUpdate와는 다르게, 지연(deferred) event 동안에 layout, paint 이후에 `useEffect`로 전달된 함수가 실행된다.
* 구독이나 event handler등의 side effects 처리에 적합하다. 왜냐하면, 대부분의 이러한 작업은 화면을 업데이트하는 작업을 블록킹하면 안된다.
* 하지만, 모든 effect가 지연될 수는 없다.
* 예를들어, DOM의 변화는 사용자가 노출된 내용의 불일치를 경험하지 않도록 다음 화면을 다 그리기 이전에 동기화되어야한다.
  * 즉, effect가 실행됨과 동시에 DOM의 변화가 일어난다면 flickering(화면 깜빡임)이 발생한다.
  * 따라서 `useLayoutEffect` 메서드를 활용하여, 동기적으로 실행시킨다.
  * passive vs active event listener의 개념과 유사하다.
* 정리
  * `useEffect`는 화면 paint 이후에 실행되기 때문에, DOM mutation에 대한 side effect가 있을 경우 깜빡임이 발생한다.
  * 데이터 fetch, event handler 등 대부분의 작업은 `useEffect` 메서드를 활용
  * `useLayoutEffect`는 화면 paint 이전, Component render 이후에 실행되기 때문에, 동기적으로 실행된다. 따라서, DOM mutation이 있더라도, 깜빡임이 발생하지 않는다.
  * 렌더링이후 DOM 요소의 값을 읽고 처리해야하는 함수가 있다면(scroll position 등)은 `useLayoutEffect` 메서드를 활용

 

## Conditionally firing an effect

---

* 기본적으로 `useEffect` 메서드는 모든 렌더링 작업 이후에 실행된다. 또한, dependency 중 하나가 변경된다면 effect는 재생성된다.
* 이러한 작업은 매번 실행될 필요가 없다.
* `useEffect` 두 번째인자인 배열에, effect가 의존하고있는 value를 전달한다.
* 해당 value가 변경될 때에만, useEffect가 실행된다.

```js
useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source],
);
```

* 시간에 따라 변화하고, effect에 사용되는 props, state와 같은 컴포넌트 스코프의 값이 배열에 포함되었는지 확인해야한다.
* 그렇지 않다면, 이전에 렌더링된 오래된 값을 참조한다.
* 함수를 다루는 상황과 array values가 너무 자주 바뀌는 상황에서 어떻게 처리할지 공부
  * [how to deal with functions](https://reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)
  * [array values change too often](https://reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)
* 만약 effect 발생이나 clean up 과정을 한번만 (mount 및 unmount 상황에서) 실행시키고 싶다면, `[]` 빈 배열을 두 번째 인자로 전달한다. 이것은 effect가 그 어떤 value(props, state 등)와도 의존하지 않는것을 의미하며 재실행이 필요없다는 것을 React에 알려준다.
* 빈 배열을 보낸다면, effect안에 있는 props나 state는 항상 초기값을 갖게된다.
* 브라우저가 painting되기 전까지는 useEffect가 지연(defer)되기 때문에 추가적인 작업 수행되지 않는다.
* [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 패키지의 [`exhaustive-deps`](https://github.com/facebook/react/issues/14920) 규칙을 사용하기를 권장하며, 의존성이 바르게 정의되지 않았다면 경고를 보낸다.
* 의존성 배열은 effect 함수 인자로 사용되지않는다. 하지만 개념적으로 그들의 표현하고자하는 것을 보여주기 때문에, effect 내에서 참조되는 모든 value는 의존성 배열에 포함되어야한다.



## how to deal with functions

---

* 의존성 배열로 부터 함수를 생략하는 것은 일반적이지 않다.

```js
function Example({ someProp }) {
  function doSomething() {
    console.log(someProp);
  }

  useEffect(() => {
    doSomething();
  }, []); // 🔴 This is not safe (it calls `doSomething` which uses `someProp`)
}
```

* 어떤 props나 state가 effect 메서드 밖에서 사용되었는지 파악하는 것은 어렵기 때문에, 내부에서 선언해야한다.

```js
function Example({ someProp }) {
  useEffect(() => {
    function doSomething() {
      console.log(someProp);
    }

    doSomething();
  }, [someProp]); // ✅ OK (our effect only uses `someProp`)
}
```

* 만약 컴포넌트 스코프에서 어떠한 value도 사용하지 않았다면, 빈 배열을 지정하는게 안전하다.

```js
useEffect(() => {
  function doSomething() {
    console.log('hello');
  }

  doSomething();
}, []); // ✅ OK in this example because we don't use *any* values from component scope
```

* `useEffect`, `useLayoutEffect`, `useMemo`, `useCallback`, `useImperativeHandle` 등의 Hook에서 마지막 인자로 의존성 배열을 지정하는 경우에, callback에 포함되거나 React data flow에 관여하는 모든 value(props, state 등)를 포함해야한다.
* 컴포넌트 스코프안에 있는 props, state, 파생된 value를 참조하지 않는 경우에만 의존성 배열에서 생략해야한다.

```js
function ProductPage({ productId }) {
  const [product, setProduct] = useState(null);

  async function fetchProduct() {
    const response = await fetch('http://myapi/product/' + productId); // Uses productId prop
    const json = await response.json();
    setProduct(json);
  }

  useEffect(() => {
    fetchProduct();
  }, []); // 🔴 Invalid because `fetchProduct` uses `productId`
  // ...
}
```

```js
function ProductPage({ productId }) {
  const [product, setProduct] = useState(null);

  useEffect(() => {
    // By moving this function inside the effect, we can clearly see the values it uses.
    async function fetchProduct() {
      const response = await fetch('http://myapi/product/' + productId);
      const json = await response.json();
      setProduct(json);
    }

    fetchProduct();
  }, [productId]); // ✅ Valid because our effect only uses productId
  // ...
}
```

* effect 메서드 내 로컬 변수를 활용하여, response를 순차적으로 실행시킬 수 있다.

```js
  useEffect(() => {
    let ignore = false;
    async function fetchProduct() {
      const response = await fetch('http://myapi/product/' + productId);
      const json = await response.json();
      if (!ignore) setProduct(json);
    }

    fetchProduct();
    return () => { ignore = true };
  }, [productId]);

```

* effect 메서드 내부로 함수를 이동할 수 없는 경우
  1. 해당 함수를 컴포넌트 외부로 옮긴다.
     * 이 경우, 함수는 어떤 props, state를 참조하지 않도록 보장하며, 의존성 배열에 있을 필요도 없다.
  2. 호출하는 함수가 pure한 계산이고 렌더링되는 동안 호출되도 안전할 경우, effect의 밖에서 호출해도 되며 반환된 값에 의존하는 effect를 만들 수 있다.
  3. 마지막으로, 의존성에 영향을 주는 함수를 추가하되, useCallback Hook으로 wrapping한다.
     * 이것은, 그 자체의 의존성이 변하지 않는한, 렌더링이 되어도 변경되지 않음을 보장한다.

```js
function ProductPage({ productId }) {
  // ✅ 모든 렌더링에서 변경되지 않도록 useCallback으로 래핑
  const fetchProduct = useCallback(() => {
    // ... productId로 무언가를 합니다 ...
  }, [productId]); // ✅ 모든 useCallback 종속성이 지정됩니다

  return <ProductDetails fetchProduct={fetchProduct} />;
}

function ProductDetails({ fetchProduct }) {
  useEffect(() => {
    fetchProduct();
  }, [fetchProduct]); // ✅ 모든 useEffect 종속성이 지정됩니다
  // ...
}

```

* 위 경우에 ProductPage의 productId props가 변경되면, ProductDetails 컴포넌트에서 refetch가 자동으로 유발된다.



## array values change too often

---

* effect dependencies가 너무 자주 변경되는 경우, 의존성 배열에서 value를 생략하는 경우가 있지만, bug가 발생한다.

```js
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1); // This effect depends on the `count` state
    }, 1000);
    return () => clearInterval(id);
  }, []); // 🔴 Bug: `count` is not specified as a dependency

  return <h1>{count}</h1>;
}

```

* 의존성 배열의 `[]` 는 effect가 mount될 때만 실행되며, re-render될 때마다 실행되는 것이 아님을 의미한다.
* 문제는 setInterval callback 내부에서 count value가 변경되지 않는다. effect callback이 처음 실행되었을 때 처럼 count 값이 0으로 된 closure를 만들었기 때문이다. 따라서 해당 callback은 `setCount(0+1)` 만 호출하므로, count값은 1을 넘지 못한다.
* 만약 count value를 의존성 배열에 추가한다면 bug는 수정할 수 있지만 모든 변경에 따라 count가 reset된다.
* 이를 해결하기 위해,  [functional update form of `setState`](https://reactjs.org/docs/hooks-reference.html#functional-updates) 을 사용할 수 있다. 현재 state를 참조하지 않고, state를 변경할 방법을 지정하게 해준다.

```js
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1); // ✅ This doesn't depend on `count` variable outside
    }, 1000);
    return () => clearInterval(id);
  }, []); // ✅ Our effect doesn't use any variables in the component scope

  return <h1>{count}</h1>;
}
```

* 이 경우 setInterval이 매초 실행되자만, setCount의 내부 호출(closure)로 count value를 업데이트할 수 있다.
* 만약 state가 다른 state에 의존하는 경우 useReducer Hook을 사용하여 state 업데이트 로직을 effect 외부로 옮긴다.
  * ex) https://adamrackis.dev/state-and-use-reducer/
* 만약 class의 함수처럼 this를 사용하고 싶다면  [ref](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables) 를 사용하여 가변 변수를 hold한다.

```js
function Example(props) {
  // Keep latest props in a ref.
  const latestProps = useRef(props);
  useEffect(() => {
    latestProps.current = props;
  });

  useEffect(() => {
    function tick() {
      // Read latest props at any time
      console.log(latestProps.current);
    }

    const id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, []); // This effect never re-runs
}
```

* 변경에 의존한다면 컴포넌트를 예측할 수 없기때문에, 대안이 없는 경우에만 사용한다.




## Reference

* [Code Squad 클린 리액트 웹프로그래밍 과정](https://codesquad.kr/page/react.html)
* [React Official Documentation](https://reactjs.org/docs/hooks-reference.html#useeffect)

