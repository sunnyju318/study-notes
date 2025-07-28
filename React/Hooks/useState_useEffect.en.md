# React Hook: useState & useEffect
## Overview: Understanding the fundamentals of useState and useEffect
>This document explains the concepts and behavior of React’s core Hooks — useState and useEffect — using a cat image API as an example.

```js

import { useState, useEffect } from 'react';
// Import necessary hooks

export default function CatPhoto() {
// Create a React component called CatPhoto

  const [catImage, setCatImage] = useState(null);
  // Set up useState
  // catImage: a getter that holds the current state (this value will be displayed on screen)
  // setCatImage: a setter function to request a state update
  // The setter doesn't change the value directly. It tells React, "Please update this state."
  // useState returns a pair: [getter, setter]

  // useState(null): sets the initial state to null
  // This means there’s no image initially, but we plan to set one later via the setter
  // So initially, catImage === null
  // Rendering <img src={catImage}/> before the image is loaded could cause an error
  // That’s why we usually display a loading message or spinner at this point

  /* Simple analogy:
     The setter is a messenger saying "The value has changed!"
     React hears that and updates its internal state,
     fills the getter with the new value, and re-renders the screen accordingly.
  */

  useEffect(() => {
    fetch("https://api.thecatapi.com/v1/images/search")
      .then(response => response.json())
      .then(data => setCatImage(data[0].url));
  }, []);
  // Basic syntax: useEffect(() => {}, [])
  // () => {...} is a callback function that runs after the component is rendered
  // A callback is a function passed as an argument to another function to run later

  /* React’s execution order for useEffect:
     1. The component function runs (returns JSX)
     2. React renders that JSX to the DOM
     3. Then the useEffect callback runs
     → useEffect is post-render logic
  */

  // fetch: sends a request to an external API to get a cat image
  // .then(response => response.json()): converts the fetched data (response) into JSON format
  // Since the response is a stream by default, we need to convert it to usable JS format

  // .then(data => setCatImage(data[0].url)):
  // The setter receives the image URL and tells React to update the state
  // React then updates the getter and re-renders the component
  // The new <img src={catImage}/> is rendered

  /* Explanation of data[0].url:
     1. data is an array of objects (cat info)
     2. [0] gets the first object
     3. .url retrieves the image URL string
  */

  // []: The dependency array tells React when to re-run useEffect
  /*
    1. [] empty array: run only once after the initial render
    2. [count]: run on first render + whenever 'count' changes
    3. No array at all: run after every re-render → usually avoided for performance
  */

  return (
    <div>
      <h1>Today's Cat</h1>
      {catImage ? (
        <img src={catImage} alt="A cute cat" />
      ) : (
        <p>Loading...</p>
      )}
    </div>
  );
}

// JSX must return a single parent element.
// Here we use <div>, but <>...</> (React Fragment) also works if no wrapper element is needed.

// {catImage ? (...) : (...)} is a ternary operator for conditional rendering.
// If catImage is not null, display the image. Otherwise, show "Loading...".

// <img src={catImage}>: catImage is managed by useState and updated via setCatImage(data[0].url).

/* Why we check for loading state:
1. useEffect runs after the first render
2. Initially, fetch hasn't completed, so catImage is still null
3. Trying to render <img src={catImage}/> immediately may cause errors
4. So we show a loading message while waiting
5. Once the data is loaded, React re-renders and shows the image
*/



```
