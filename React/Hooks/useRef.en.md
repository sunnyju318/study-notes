# React Hook: useRef
## Overview: Understanding the basics of how useRef works in React.
>This document explains how one of React's core hooks, useRef, functions and where it's commonly used.
>What is useRef? (Ref = Reference)
useRef has two main roles:
DOM Reference for directly accessing DOM elements
Persistent Storage for storing values that don’t affect rendering
When a value changes in useState, the component re-renders.
But when a value in useRef changes, no re-render occurs.
It works like a personal memo pad inside the component, retaining values even between renders.
It's also commonly used for directly accessing DOM elements,
such as focusing an input or tracking scroll position.
Example: Movie Seat Booking System
Already-booked seats: managed with useState (since they must be displayed → re-rendering is needed)
Seats the user is temporarily selecting: managed with useRef (not yet finalized → no visual effect needed)
→ Once the user confirms, we can move that data from useRef to useState to reflect it on the UI.

```js
import React, { useRef, useState, useEffect } from 'react';

export default function RefExample() {
  const inputRef = useRef(null); // DOM reference

  // Explanation:
  // useRef(null) creates a reference object.
  // Initially, inputRef.current is null.
  // After we attach it to <input ref={inputRef} />,
  // inputRef.current will point to the actual DOM element.
  // This allows us to control the input directly (e.g., focus, set value, etc.)

  const intervalRef = useRef(null); // persistent storage (non-visual)

  /*
  setInterval(() => {
    console.log('Runs every second');
  }, 1000);

  - `setInterval`: a built-in JavaScript function that repeats execution.
  - It returns a numeric timer ID.
  - To stop it later, you need to call `clearInterval(id)`.

  The ID itself doesn’t affect the screen/UI.
  So, instead of `useState`, we store it with `useRef` to avoid unnecessary re-renders.
  */

  const [count, setCount] = useState(0); // For displaying count

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setCount(prev => prev + 1);
    }, 1000);

    // Clean-up function
    return () => clearInterval(intervalRef.current);
  }, []);

  /*
  `return () => {}` inside useEffect is called a cleanup function.
  It runs automatically when the component unmounts or before useEffect runs again.

  Example:
  If <Home /> is visible and you navigate to <About />, the <Home /> component unmounts.
  So this cleanup runs at that moment.

  This is important to avoid issues like memory leaks:
  If `setInterval` keeps running even after the component disappears,
  it’s doing work you no longer need.
  */

  const handleFocus = () => {
    if (inputRef.current) {
      inputRef.current.focus(); // puts the cursor inside the input field
    }
  };

  const stopCounting = () => {
    clearInterval(intervalRef.current); // manually stop count increment
  };

  return (
    <div style={{ padding: '2rem' }}>
      <h2>useRef Example</h2>
      <p>Count: {count}</p>

      {/* DOM reference to input */}
      <input
        ref={inputRef}
        type="text"
        placeholder="Click button to focus"
        style={{ padding: '0.5rem', marginRight: '1rem' }}
      />

      <button onClick={handleFocus}>Focus Input</button>
      {/* When clicked, focuses the input field */}

      <button onClick={stopCounting} style={{ marginLeft: '1rem' }}>
        Stop Counting
      </button>
      {/* When clicked, stops the auto-incrementing counter */}
    </div>
  );
}

```
