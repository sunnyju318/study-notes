# React Hook : useState & useEffect

## Overview : useState와 useEffect의 기본원리 이해하기

>> 이 문서는 React의 대표적인 Hook인 useState와 useEffect의 개념과 작동 원리를 고양이 이미지 API를 예시로 들어 설명합니다.

```js


import { useState, useEffect } from 'react';
// 필요한 hooks import 하기

export default function CatPhoto() {
// CatPhoto 라는 React 컴포넌트 만들기

  const [catImage, setCatImage] = useState(null);
  // useState 세팅하기
  // catImage : 현재 상태값을 읽는 getter (기본적으로 이 변수 내용이 화면에 출력됨)
  // setCatImage : 상태를 변경 요청하는 setter 함수
  // setter는 값을 직접 저장하지 않음. React에게 이 상태를 바꿔줘 라고 요청하는 함수임
  // useState는 이 두가지를 한쌍으로 제공함
  
  // useState(null) : 초기 상태값 선언
  // 지금은 상태값이 없는 null 상태이며 나중에 데이터를 받아서 setter로 넣을 겁니다 라는 초기상태 선언
  // catImage === null
  // 즉, 아직 사진이 없으니까 <img src={catImage}/>를 바로 보여주면 에러가 날 수 있음
  // 그래서 보통 이 시점에 "로딩중.." 혹은 스피너(프리로더의 한 종류) 같은걸 보여준다.
  
  /* 이해하기 쉬운 예제 : setter는 React에게 "값이 자뀌었어요!" 라고 알리는 메신저이며
   React는 그 말을 듣고 내부 상태를 업데이트 한뒤
   getter에 최신 값을 담아주고 화면도 다시 그려주는 역할을 한다. 
   */

  useEffect(() => {
    fetch("https://api.thecatapi.com/v1/images/search")
      .then(response => response.json())
      .then(data => setCatImage(data[0].url));
  }, []);
  // 기본 씬택스 : useEffect(() => {}, [])
  // () => {...} 화살표함수, 컴포넌트가 렌더링된 후 실행될 call back 함수이다.
  // call back 함수 : 나중에 실행될 함수를 다른 함수에 인자로 넘기는 것을 말한다.
  // 이 일이 끝나면 얘(함수)를 실행시켜줘 라고 하는 함수, 특정시점에 실행될 함수를 미리 전달하는것.
  // 자바스크립트가 그 특정 시점이 되면 실행
  
  /* 쉬운설명 useEffect가 실행되는 React의 동작순서
  1. 컴포넌트 함수가 실행됨(즉, jsx를 리턴함)
  2. 리엑트가 그 jsx를 DOM에 렌더링함(화면에 그림)
  3. 그 후에 useEffect 안에 있는 콜백함수가 실행됨.
  즉 useEffect는 렌더링이 끝난 다음에 실행되는 후처리 코드라고 생각하면 된다.
  */
  
  // fetch : 외부 API에서 고양이 사진 데이터를 요청
  // .then(response => response.json()) : fetch에서 받아온 데이터(response)를 
  // JSON형식(자바스크립트 객체)로 변환해 달라는 뜻이다. 
  // 서버의 응답은 보통 스트림(데이터 덩어리) 상태이므로 바로 사용할수가 없다. 그래서 JSON으로 반환해 쓴다.
  // response 개발자가 붙인 변수명이며 자주 쓰이는 이름이다.
  
  // .then(data => setCatImage(data[0].url)); : data(JS 객체)를 setter인 setCatImage로 전달하여
  // setter가 React에게 값의 상태가 바뀌었음을 알림.
  // React는 상태(catImage)가 바뀌었음을 감지하여 getter(catImage)에 최신값을 넣고 다시 컴포넌트를 렌더링함.
  // 화면에 <img src={catImage}/>가 새로 그려짐
  
  /* (data[0].url) : 
  1. data(앞서 받은 고양이 정보가 담긴 JSON 객체 배열) 
  2. [0] : 배열의 첫번째 고양이 정보(객체)
  3. url : 그 고양이의 이미지주소(문자열)
  */
  
  // [] : useEffect()는 렌더링이 끝난 후에 실행되는 함수이고, 
  // []는 그 후에 언제 이 useEffect()를 다시 실행할지 결정하는 트리거 목록이다.
  /*
  1. [] 빈 배열 : 렌더링이 끝난 후 처음 딱 한번만 실행, 처음 화면에 나타났을때만.
  2. [count] 값이 있는 경우 : 컴포넌트가 처음 렌더링될때 실행 + count가 바뀔때마다 다시 실행
  3. x [] 이거 자체가 없는경우 : 컴포넌트가 리렌더링될 때마다 항상 실행되므로 성능에 문제가 생겨 안씀, 주의.
  */

  return (
    <div>
      <h1>오늘의 고양이</h1>
      {catImage ? (
        <img src={catImage} alt="고양이" />
      ) : (
        <p>로딩 중...</p>
      )}
    </div>
  );
}

// 여러 요소가 들어갈땐 반드시 하나의 부요소로 감쌀것. 
// 여기서는 <div>이며 의미없는 태그를 반복하기 싫은경우 <></>도 가능.
// {catImage ? (...) : (...)} : 자바스크립트에서 흔히 쓰는 조건부 렌더링 삼항연산자(ternary operator).
// catImage(상태값이 null이 아니라면, 즉 이미지 주소가 있다면) (<img>) 참일때 실행할 코드이며
// : (<p>로딩 중...</p>) 없을경우 거짓일때 실행할 코드.

// <img src={catImage}> : catImage는 useState로 관리되는 상태값(getter)이며 
// 앞서 setCatImage(data[0].url)로 setter가 받은 값이다.
// 즉 고양이 사진의 url이 들어있다.

/* 로딩중 이라는 조건부에 관하여.
1. useEffect는 컴포넌트가 렌더링 끝난 뒤에 실행이 된다.
2. 따라서 처음 jsx를 화면에 그릴 때는 아직 fetch가 안 끝났다.
3. 그래서 catImage는 처음에는 null일수밖에 없다.
4. 그 상태에서 <img src={catImage} />를 하면 가져올 데이터가 없어 에러가 날수 있다.
5. 그래서 조건부 렌더링으로 "로딩중"을 먼저 보여주고, 데이터가 오면 <img>로 바뀌도록 처리하는 것이다.
*/

```
