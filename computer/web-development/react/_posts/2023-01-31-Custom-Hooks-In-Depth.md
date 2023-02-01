---
title: "[React] Custom Hooks In Depth"
---

# Exploring Code Reuse

# Revisiting Custom Hooks

Custom Hooks
- Functions that contain some reusable logic
- Cusom hooks usually reuse built-in hooks (like `useState`, `useEffect`)
- Usually easiest to extract logic into a hook, rather than making a hook first

The Plan
1. Make a tiny demo component with a tiny bit of logic
2. Learn a design process to extract that logic into a custom hook
3. Go back to SortableTable and repeat the design process

Sample Component

![Component overview](https://i.imgur.com/fxx0bXu.png)
- Make a component that shows a counter
- It should console log the count every time it changes (`useEffect`)
- It should accept an `initialCount` as a prop

# Creating the Demo Component

```jsx
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

The CounterPage component has four general sections of code inside of it.

![When to create a hook](https://i.imgur.com/yyyeISN.png)
- count
- `useEffect`
- `handleClick`
- JSX

How to?
1. Find code in a component related to a single piece of state
2. Copy paste it all into a helper function
3. Fix all the broken references
4. You now have a hook

# Hook Creation Process in Depth

Brute-Force Hook Creation
1. Make a function called `useSomething`
2. Find all the non-JSX expressions that refer to 1-2 related pieces of state
3. Cut them all out, paste them into `useSomething`
4. Find `not defined` errors in your component
5. In your hook, return an object that contains the variables the component needs
6. In the component, call your hook. Destructure the properties the component needs
7. Find `not defined` errors in the hook. Pass the missing variables in as arguments to the hook
8. Rename the hook to something more meaningful
9. Rename returned properties to something more descriptive

```jsx
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

```jsx
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

![Result](https://imgur.com/v1yNbry.png)

# Making a Reusable Sorting Hook
