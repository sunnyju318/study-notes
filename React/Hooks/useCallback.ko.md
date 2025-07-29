# React Hook : useCallback
## Overview : useCallback의 기본원리 이해하기
>컴포넌트가 리렌더링 될때.
1. 리렌더링 시에 일반 함수는 매번 새로 만들어진다.
2. useCallback 을 쓰면 이전에 만든 함수 정의 자체를 저장해뒀다가 
    리렌더링 할때도 그 함수를 새로 만들지않고 재사용해서 불필요한 연산을 막는것이다.
3. 그렇게 해서 기억해둔 동일한 함수의 객체를 다시 넘겨주는 것이다.
    * 중요한것은 이걸로 성능을 최적화할수 있다는 것이다.

```js
// ParentComponent.jsx

// 부모 컴포넌트의 버튼과 자식 컴포넌트의 버튼은 별개이다.
// 다만 useCallback의 효과를 확인하려면 부모가 리렌더링 되는 상황이 필요하다.
// 부모가 리렌더링해도 props에 변화가 없으면 자식은 리렌더링 되지 않는다는 상황을 보기 위해서다.
// 부모 컴포넌트의 버튼은 단지 부모 컴포넌트를 리렌더링시키기 위한 트리거 역할일 뿐이다.
// 부모가 리렌더링 될때마다 handleClick 함수가 useCallback 없이 쓰면 매번 새로 만들어지며 자식도 리렌더링 된다.
// useCallback을 쓰면 부모에게서 기억된 함수를 재사용하며 자식은 리렌더링을 하지 않는다.
// 그래서 실험용으로 +1 버튼을 넣은 것이다.

import React, { useState, useCallback } from 'react';
import Child from './Child';
//Child 컴포넌트에 props를 넘겨주기 위해 import 함

export default function ParentComponent() {
  const [count, setCount] = useState(0);

  // 버튼 클릭할 때 자식에게 넘겨줄 함수
  const handleClick = useCallback(() => {
    console.log('Child button clicked!');
    // 자식버튼을 누를때마다 찍힌다 : 이벤트 실행 확인용
  }, []); // 의존성이 없으므로 한 번만 생성됨
  // [ ] :의존성 배열(Dependency Array), 언제 리렌더링(또는 다시 실행)할지 정하는 역할

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>+1</button>

      <Child onChildClick={handleClick} />
    </div>
  );
}

/*
1. 부모는 handleClick라는 함수를 가지고 있음.
2. <Child onChildClick={handleClick} />을 통해 자식에게 onChildClick라는 이름으로 함수를 전달
3. 자식은 ({onChildClick})로 props에서 그걸 꺼내서 자기 버튼 onClick={onChildClick}로 연결함
4. 버튼을 누르면 결국 부모의 함수가 실행됨.
*/
```

```js
// Child.jsx

/*
부모로 부터 받은 props가 변하지 않는 한
자식 컴포넌트는 변동이 없다고 판단하고
처음 렌더링된 모습을 그대로 유지한다.
버튼을 아무리 눌러도 그 겶과는 부모로부터 전달받은 함수 실행일뿐
자식 자신의 리렌더링은 발생하지 않는다.
*/
import React from 'react';

export default React.memo(function Child({ onChildClick }) {
// React.memo : props가 바뀌지 않았따면 해당 함수형 컴포넌트를 다시 렌더링하지 않도록 막아주는 리액트의 최적화 도구
// props 값이 변했는지를 비교함, useCallback 덕분에 넘겨준 함수가 같은 참조값을 유지하므로 props가 변하지 않았다 판단함
// 현재 Child 컴포넌트가 React.memo로 감싸여 있음, 
// 부모로부터 props {onChildClick}을 받아옴
  console.log('Child rendered');
  // 자식 컴포넌트가 렌더링 될때마다 찍힌다. : 렌더링 여부 확인용

  return (
    <div style={{ marginTop: '20px' }}>
      <button onClick={onChildClick}>Click Me (Child)</button>
    </div>
    // 부모로 부터 받아온 props를 자식 컴포넌트 버튼의 onClick에 연결함
  );
});

```
