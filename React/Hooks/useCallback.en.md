# React Hook : useCallback
## Overview: Understanding the core concept of useCallback
>When a component re-renders:

Regular functions are recreated every time during re-render.
With useCallback, a function definition is memoized—stored so that it's not recreated on every render.
Instead, the previously created function object is reused.
This helps optimize performance by preventing unnecessary computations and component updates.

```js
// ParentComponent.jsx

// The parent and child buttons are separate.
// To demonstrate the effect of useCallback, we need a situation where the parent re-renders.
// Even if the parent re-renders, the child won’t re-render unless its props have changed.
// The +1 button in the parent is used to trigger the parent’s re-render without changing any props passed to the child.
// Without useCallback, the handleClick function would be recreated on every render, causing the child to re-render due to receiving a new prop reference.
// With useCallback, the function reference remains the same, so the child doesn’t re-render.
// That’s why the +1 button was added—for experimental observation.

import React, { useState, useCallback } from 'react';
import Child from './Child'; // Importing to pass props to the child

export default function ParentComponent() {
  const [count, setCount] = useState(0);

  // Function passed to the child
  const handleClick = useCallback(() => {
    console.log('Child button clicked!'); // Logged every time the child button is clicked
  }, []); // No dependencies → function created only once

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>+1</button>
      <Child onChildClick={handleClick} />
    </div>
  );
}

/*
1. The parent defines the `handleClick` function.
2. It is passed down to the child via `<Child onChildClick={handleClick} />`.
3. The child receives it as a prop and assigns it to its button’s `onClick`.
4. When the button is clicked, the parent’s function is executed.
*/

```
```js
// Child.jsx

// Unless the props passed from the parent to the child change,
// the child component will not re-render.
// Clicking the child button triggers the function,
// but doesn’t affect the child’s rendering itself.
// React.memo optimizes the child component by skipping re-renders
// if the received props haven’t changed.
// useCallback ensures the function reference stays stable across renders.

import React from 'react';

export default React.memo(function Child({ onChildClick }) {
  // React.memo prevents re-rendering if props haven’t changed.
  // Thanks to useCallback, the function reference stays the same,
  // so props are considered unchanged and the child doesn't re-render.

  console.log('Child rendered'); // Logs whenever the child is re-rendered

  return (
    <div style={{ marginTop: '20px' }}>
      <button onClick={onChildClick}>Click Me (Child)</button>
    </div>
  );
});

```
