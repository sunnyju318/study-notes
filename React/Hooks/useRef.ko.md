# React Hook : useRef
## Overview : useRef의 기본원리 이해하기

>이 문서는 React의 대표적인 Hook인 useRef의 작동원리를 설명합니다.

```js
/* useRef란? (Ref : Reference, 참조값)

- useRef의 2가지 역할 : 
DOM 조작용 참조(DOM Reference), 
렌더링과 무관한 값 저장소(Persistent Storage)

- useState는 값이 바뀌면 컴포넌트가 **리렌더링**되지만, 
  useRef는 값이 바뀌어도 리렌더링되지 않는다.
- 마치 "리액트 컴포넌트 안의 개인 메모장" 같은 역할로, 
  렌더링 사이에서도 값을 계속 **기억**할 수 있다.
- 또한 useRef는 DOM 요소에 직접 접근할 때도 사용된다 
  (예: input 포커스, 스크롤 위치 제어 등).

예시: 영화 좌석 예매 시스템
- 이미 결제된 좌석 상태: `useState`로 관리 (화면에 보여져야 하므로 리렌더링 필요)
- 유저가 임시로 클릭해서 고려 중인 좌석들: `useRef`로 관리 (화면에 영향 없음, 임시 저장)
→ 최종 결제가 되면, useRef로 저장해놨던 선택 정보를 useState로 옮겨서 반영한다.
*/


import React, { useRef, useState, useEffect } from 'react';
// 필요한 hooks import 하기

export default function RefExample() {
// React 컴포넌트 생성

  const inputRef = useRef(null);  // DOM 조작용 참조(DOM Reference)
  // useRef(null)을 inputRef로 정의함
  // inputRef.current는 처음엔 null임.
  // 추후에 <input ref={inputRef} /> 와 같은 코드를 통해 해당 <input>의 DOM 요소를 가리키게 된다.
  // 즉 inputRef는 해당 input 태그의 DOM 요소에 직접 접근할수 있게 해주는 참조 변수이다.
  
  /* 쉬운설명 
  1. useRef는 React에서 특정 DOM 요소에 접근할수 있도록 해주는 방법이다.
  2. React에서는 기본적으로 DOM에 직접 접근을 못한다.
  (React는 "가상DOM"으로 렌더링을 관리하므로 직접 조작하는걸 지양한다)
  3. 그래서 useRef() 를 사용해서 특정 DOM요소에 참조를 걸어줘야 한다.
  : const inputRef = useRef(null); // 빈 참조 생성 (초기값 null)
  4. 이걸 실제 JSX에서 ref 속성에 연결한다.
  : <input ref={inputRef} /> 
  : 여기서부터는 inputRef.current가 null이 아닌 해당 input의 DOM 요소를 담는 상자가 된다.
  5. 이후에 이렇게 조작 가능하다.
  : inputRef.current.focus(); // input에 포커스 주기
  : inputRef.current.value = "Hello"; // 값 직접 설정하기
    */
  
  const intervalRef = useRef(null); // 렌더링과 무관한 값 저장소(Persistent Storage)    
  /*
  const id = setInterval(() => {
  console.log('1초마다 실행됨');
  }, 1000);
  
   - setInterval : 자바스크립트 기본함수이다, 일정 시간마다 어떤 함수를 반복 실행시킨다.
  위 코드는 1초마다 console.log를 실행시키는 함수이다.
  이 id를 나중에 중지하려면 아래와 같은 코드를 써야한다.
  
  clearInterval(id);
	setInterval()을 실행하면 브라우저가 타이머id를 하나 준다. 
	이 아이디는 그냥 숫자인데 이걸 기억해놔야 나중에 clearInterval(id)로 멈출수 있다.
	
	그런데 이 아이디는 그저 setInterval과 clearInterval을 조작하는데 필요한 숫자일뿐
	실제 화면면에는 영향을 주지 않는다. 그래서 굳이 useState로 관리하면 불필요한 리렌더링이 생기므로
	useRef로 저장하는 것이다.
  */
  
  const [count, setCount] = useState(0); 
  // 3. 카운트 표시용, 초기값은 0

  useEffect(() => {
    intervalRef.current = setInterval(() => {
    // setInterval의 ID를 ref에 저장해서 나중에 멈출수 있도록 함
      setCount(prev => prev + 1);
    }, 1000);
  // 자동 카운트 증가 (1초마다)
  /* intervalRef.current = setInterval 
  : setInterval의 아이디 값을 intervalRef.current에 저장
  : intervalRef.current = useRef(null)
  */
 /* setCount(prev => prev + 1); 
  : prev는 이전 count(getter) 값이며 setCount(setter).
  : count가 계속해서 1,2,3,4... 올라가게 해줌 
  */
  /*
  setCount(prev => prev + 1); (o)
  setCount(count + 1); (x)
  : count값을 바로 쓰면 정확하지 않은 값일수 있기 때문에 예상치못한 버그가 발생할수 있다.
  : 그래서 항상 현재값을 기반으로 다음값 계산 방식을 쓴다. 
  */
    
    return () => clearInterval(intervalRef.current);
  }, []);
  /*
  return () => { ... } : useEffect에서 이것은 clean-up function이라고 부른다.
  이 함수는 컴포넌트가 사라질때(unmount) 자동으로 실행된다.
  또는 useEffect가 다시 실행되기 직전에도 실행된다.
  예를들어 : /home에서는 <Home/>컴포넌트가 보이고 /about으로 이동하면 <About/>
  컴포넌트가 보이는데 이때 <Home/>는 unmount되므로 클린업 함수가 실행된다.
  만약 컴포넌트는 눈에 보이지 않는데 setInterval같은게 보이지않는 곳에서 계속 실행되면
  불필요한 작업이 계속 일어나서 메모리 누수(memory leak) 문제가 생기므로 정리해주는게 좋다.
  */
  // [ ] : 처음 나타날때 실행, 사라질때 정리
  // 컴포넌트 unmount 시 interval 제거
  
  /* ======= 여기까지 useEffect ========= */
  

  // 버튼 클릭 시 input에 포커스
  const handleFocus = () => {
    if (inputRef.current) {
    // 만약 inputRef.current 이 존재한다면 아래 줄을 실행시켜라.
      inputRef.current.focus();
      // 이 줄이 실행되면 inputRef가 참조하는 input 태그에 커서를 자동으로 위치시켜 달라는 뜻
    }
  };

  const stopCounting = () => {
    clearInterval(intervalRef.current);
  };
  // 카운트 중지
  // 이건 사용자가 버튼을 눌렀을 때 실행되는 함수이다.
  // 1초마다 증가하는 count를 중간에 멈추고 싶을때 직접 멈추는 것이다.
  // 수동 트리거

  return (
    <div style={{ padding: '2rem' }}>
      <h2>useRef Example</h2>
      <p>Count: {count}</p>
		  {/* inputRef를 통해 input 요소의 DOM에 직접 접근하여 조작할 수 있게 함*/}
      <input
        ref={inputRef}
        type="text"
        placeholder="Click button to focus"
        style={{ padding: '0.5rem', marginRight: '1rem' }}
      />
      <button onClick={handleFocus}>Focus Input</button>
      {/* 사용자가 버튼을 클릭하면 handleFocus를 실행시켜 커서를 input 태그에 자동으로 위치시키기*/}
      <button onClick={stopCounting} style={{ marginLeft: '1rem' }}>Stop Counting</button>
      {/* 사용자가 버튼을 클릭하면 stopCounting를 실행시켜 +1씩 증가하는 count를 멈추기*/}
    </div>
  );
}

```
