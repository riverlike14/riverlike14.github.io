---
title: "[React] Custom Hooks In Depth"
---

# App Overview

![Result](https://imgur.com/v1yNbry.png)

Simple Counter.
- Make a component that shows a counter.
- It should `console.log` the count every time it changes.
- It should accept an `initialCount` as a prop.

## Features

- Learn a design process to extract a logic into a custom hook.

# Revisiting Custom Hooks

Custom Hooks.
- Functions that contain some reusable logic.
- Cusom hooks usually reuse built-in hooks, like `useState` or `useEffect`.
- Usually it is easy to extract logic into a hook, rather than making a hook first.


## Creating the Demo Component

First create a demo component.
- ```jsx
  // "./pages/CounterPage.js"
  import { useState, useEffect } from "react";
  import Button from "../components/Button";

  const CounterPage = ({ initialCount }) => {
    const [count, setCount] = useState(initialCount);

    useEffect(() => {
      console.log(count);
    }, [count]);

    const handleClick = () => {
      setCount(count + 1);
    }

    return (
      <div>
        <h1>Count is {count}</h1>
        <Button onClick={handleClick} >Increment</Button>
      </div>
    );
  };

  export default CounterPage;
  ```

# Custom Hook Creation

![When to create a hook](https://i.imgur.com/yyyeISN.png)

The `CounterPage` component has four general sections of code inside of it.
- `count`
- `useEffect`
- `handleClick`
- JSX


## Three Steps to Create a Custom Hook

In general, follow these steps and create a custom hook.
1. Find code in a component related to a single piece of state.
2. Copy paste it all into a helper function.
3. Fix all the broken references.

## Hook Creation Process in Detail

Real process of creating custom hooks.
1. Make a function called `useSomething`.
2. Find all the non-JSX expressions that refer to 1-2 related pieces of state.
3. Cut them all out, paste them into `useSomething`.
4. Find `not defined` errors in your component.
5. In your hook, return an object that contains the variables the component needs.
6. In the component, call your hook. Destructure the properties the component needs.
7. Find `not defined` errors in the hook. Pass the missing variables in as arguments to the hook.
8. Rename the hook to something more meaningful.
9. Rename returned properties to something more descriptive.

You may end up with the following files.
- ```jsx
  // "./hooks/use-counter.js"
  import { useState, useEffect } from "react";

  const useCounter = (initialCount) => {
    const [count, setCount] = useState(initialCount);

    useEffect(() => {
      console.log(count);
    }, [count]);

    const increment = () => {
      setCount(count + 1);
    }

    return {
      count: count,
      increment: increment
    }
  };

  export default useCounter;
  ```
- ```jsx
  // "./pages/CounterPage.js"
  import Button from "../components/Button";
  import useCounter from "../hooks/use-counter";

  const CounterPage = ({ initialCount }) => {
    const { count, increment } = useCounter(initialCount);

    return (
      <div>
        <h1>Count is {count}</h1>
        <Button onClick={increment} >Increment</Button>
      </div>
    );
  };

  export default CounterPage;
  ```
