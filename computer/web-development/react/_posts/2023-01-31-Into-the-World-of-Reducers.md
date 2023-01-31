---
title: "[React] Into the World of Reducers"
---

# App Overview

useReducer
- Goal is very similar to usestate
- it creates some state inside of a component
- whenever that state changes the component is going to rerender
- think of this hook as being kind of similar to useState

![useReducer](https://i.imgur.com/txbbpcC.png)

Refactor the CounterPage to use useState without any custom hooks
&rarr;
Refactor with a new hook called useReducer

useReducer is a different way of handling state in a component.

Goal here is to get some prep for Redux.

![Counter preview](https://i.imgur.com/Vp589dM.png)

# Adding the Form

```jsx
// "./pages/CounterPage.js"
import { useState } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const CounterPage = ({ initialCount }) => {
  const [count, setCount] = useState(initialCount);

  const increment = () => {
    setCount(count + 1);
  }

  const decrement = () => {
    setCount(count - 1);
  }

  return (
    <Panel className="m-3">
      <h1 className="text-lg">Count is {count}</h1>
      <div className="flex flex-row">
        <Button onClick={increment} >Increment</Button>
        <Button onClick={decrement} >Decrement</Button>
      </div>

      <form>
        <label>Add a lot!</label>
        <input type="number" className="p-1 m-3 bg-gray-50 border border-gray-300" />
        <Button>Add it!</Button>
      </form>
    </Panel>
  );
};

export default CounterPage;
```

# More on the Form

![event.target.value is a string](https://imgur.com/8n33VrM.png)
`event.target.value` returns a string

use `parseInt` to fix this

![NaN is returned](https://imgur.com/IJn37ky.png)
When input is removed, NaN is returned
- use `const value = parseInt(event.target.value) || 0`

![0 keeps showing up](https://imgur.com/sfsaVN2.png)
0 keeps showing up
- `<input value={valueToAdd || ""} />`

```jsx
// "./pages/CounterPage.js"
import { useState } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const CounterPage = ({ initialCount }) => {
  const [count, setCount] = useState(initialCount);
  const [valueToAdd, setValueToAdd] = useState(0);

  const increment = () => {
    setCount(count + 1);
  }

  const decrement = () => {
    setCount(count - 1);
  }

  const handleChange = (e) => {
    const value = parseInt(e.target.value) || 0;
    setValueToAdd(value);
  }

  const handleSubmit = (e) => {
    e.preventDefault();
    setCount(count + valueToAdd);
    setValueToAdd(0);
  }

  return (
    <Panel className="m-3">
      <h1 className="text-lg">Count is {count}</h1>
      <div className="flex flex-row">
        <Button onClick={increment} >Increment</Button>
        <Button onClick={decrement} >Decrement</Button>
      </div>

      <form onSubmit={handleSubmit}>
        <label>Add a lot!</label>
        <input
          value={valueToAdd || ""}
          onChange={handleChange}
          type="number"
          className="p-1 m-3 bg-gray-50 border border-gray-300"
        />
        <Button>Add it!</Button>
      </form>
    </Panel>
  );
};

export default CounterPage;
```

# `useReducer` in Action

`useState`
- Absolutely find hook to use whenever a component needs state

`useReducer`
- Alternative to `useState`
- Produces state
- Changing this state makes component rerender
- Useful when you have several different closely-related pieces of state
- Useful when future state values depend on the current state

![useState and useReducer are very similar](https://imgur.com/O2P3me8.png)
- useState and useReducer are very similar
  - State variable
  - Function to change state
  - Initial value for this state

![Community convention of useState and useReducer](https://i.imgur.com/ehugldS.png)
- useState
- Each piece of state defined as a separate variable
- useReducer
- **All** state for the whole component defined in a single object

- use `state.count` instead of `count`
- use `state.valueToAdd` instead of `state.valueToAdd`

```jsx
// "./pages/CounterPage.js"
import { useState, useReducer } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const reducer = (state, action) => {
  //
}

const CounterPage = ({ initialCount }) => {
  // const [count, setCount] = useState(initialCount);
  // const [valueToAdd, setValueToAdd] = useState(0);
  const [state, dispatch] = useReducer(reducer, {
    count: initialCount,
    valueToAdd: 0
  });

  const increment = () => {
    // setCount(count + 1);
  }

  const decrement = () => {
    // setCount(count - 1);
  }

  const handleChange = (e) => {
    const value = parseInt(e.target.value) || 0;
    // setValueToAdd(value);
  }

  const handleSubmit = (e) => {
    e.preventDefault();
    // setCount(count + valueToAdd);
    // setValueToAdd(0);
  }

  return (
    <Panel className="m-3">
      <h1 className="text-lg">Count is {state.count}</h1>
      <div className="flex flex-row">
        <Button onClick={increment} >Increment</Button>
        <Button onClick={decrement} >Decrement</Button>
      </div>

      <form onSubmit={handleSubmit}>
        <label>Add a lot!</label>
        <input
          value={state.valueToAdd || ""}
          onChange={handleChange}
          type="number"
          className="p-1 m-3 bg-gray-50 border border-gray-300"
        />
        <Button>Add it!</Button>
      </form>
    </Panel>
  );
};

export default CounterPage;
```

- useReducer &rarr; debugging is much easier.
  - Instead of console.log(var1, var2, ...), simply console.log(state)

# Rules of Reducer Functions
