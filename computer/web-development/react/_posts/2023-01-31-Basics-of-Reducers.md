---
title: "[React] Basics of Reducers"
---

# App Overview

![Counter Preview](https://imgur.com/mhRo4AG.png)

## Features

- Refactor counter component with `useReducer`.
- Goal is very similar to usestate.
  - It creates some state inside of a component.
  - Whenever that state changes the component is going to rerender.
  - Think of this hook as being kind of similar to useState.
- Compare `useState` and `useReducer`.
- Learn community convention of `useReducer` design patterns.

## Library
- `immer`

## Counter Component Design

![Counter preview](https://i.imgur.com/Vp589dM.png)

# Common Structure of Counter

Initial boilerplate of `CounterPage.js`
- Update when the form is submitted.
  - Set input value to 0.
  - Add to the count value.
- ```jsx
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

## Change Input Value Type

<!-- ![event.target.value is a string](https://imgur.com/8n33VrM.png) -->

![NaN is returned](https://imgur.com/IJn37ky.png)

Input element receives data as a string.
- `event.target.value` is a string.
- Use `parseInt` to change the value into number type.
- When input text is removed, change the input value to 0.
  - Otherwise the value will be `NaN`.
- Use `const value = parseInt(event.target.value) || 0`

## 0 Keeps Showing Up

![0 keeps showing up](https://imgur.com/sfsaVN2.png)

- Input value cannot be removed since `valueToAdd` is 0 by default.
- Conditionally render when `valueToAdd` is not zero.
- ```jsx
  // "./pages/CounterPage.js"
  //...
  const CounterPage = ({ initialCount }) => {
    const [count, setCount] = useState(initialCount);
    const [valueToAdd, setValueToAdd] = useState(0);
    //...
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
        /* ... */

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

# From `useState` To `useReducer`

`useState`
- Absolutely fine hook to use whenever a component needs state.

`useReducer`
- Alternative to `useState`.
  - Produces state.
  - Changing this state makes component rerender.
- Useful when you have several different closely-related pieces of state.
- Useful when future state values depend on the current state.

## Compare `useState` and `useReducer`

![useState and useReducer are very similar](https://imgur.com/O2P3me8.png)

Both `useState` and `useReducer` share three concepts.
- State variable.
- Function to change state.
- Initial value for this state.

**Different Community Convention**

![Community convention of useState and useReducer](https://i.imgur.com/ehugldS.png)

- When using `useState`, each piece of state is defined as a separate variable.
- On the other hand, a **single** object manages the whole state when using `useReducer`.
  - **All** states for the whole component should be defined in the single object.
  - `state.count` instead of `count`.
  - `state.valueToAdd` instead of `valueToAdd`.
- Debugging gets much easier.
  - Simply `console.log(state);` instead of `console.log(var1, var2, ...);`.
- ```jsx
  // "./pages/CounterPage.js"
  import { useState, useReducer } from "react";
  //...
  const reducer = (state, action) => {
    ;
  }

  const CounterPage = ({ initialCount }) => {
    // const [count, setCount] = useState(initialCount);
    // const [valueToAdd, setValueToAdd] = useState(0);
    const [state, dispatch] = useReducer(reducer, {
      count: initialCount,
      valueToAdd: 0
    });
    //...
  };

  export default CounterPage;
  ```

## State Updates with `useReducer`

![Flow of redux](https://i.imgur.com/7p7RXXh.png)

When you call `dispatch`, react is gonna go and find **`reducer` function** and run it.
- The first argument of `reducer` is current state that is being maintained by reducer.
- The second argument of `reducer` is an action object, and you can pass whatever you want through `dispatch`.
  - More than one element in `dispatch` are ignored.
  - The argument passed to `dispatch` shows up in `reducer` as the second argument, `action`.
- Whatever the `reducer` returns is the new state.
- ```jsx
  // "./pages/CounterPage.js"
  //...
  const reducer = (state, action) => {
    return {
      ...state,
      count: state.count + 1
    }
  }

  const CounterPage = ({ initialCount }) => {
    //...
    const increment = () => {
      // setCount(count + 1);
      dispatch();
    }
    //...
  };

  export default CounterPage;
  ```

## Rules around Reducer Functions

- Whatever `reducer` returns will be your new state.
- If `reducer` returns nothing, then your state will be *`undefined`!*
- **No async/await, no requests, no promises, no outside variables.**
- **Like almost everywhere else in React, don't directly modify the state object!**

# Understanding Action Objects

![Community solution of redux desigh](https://i.imgur.com/bXPH71h.png)
- When we call `dispatch()`, we need to pass along some information to tell the reducer *how the state should be updated.*
- The React community has come up with a convention.
  - This is a very common commounity convention, not a requirement.

## Action Object Convention

**When we need to modify state, we will call `dispatch` and *always* pass in an `action` object.**
- The `action` object will *always* have a string property called **`type`**. This tells the reducer what state update it needs to make.
- If we need to communicate some data to the reducer, it will be placed on the **`payload`** property of the `action` object.
- ```jsx
  // "./pages/CounterPage.js"
  //...
  const reducer = (state, action) => {
    switch (action.type) {
      case "increment":
        return {
          ...state,
          count: state.count + 1
        };
      case "change-value-to-add":
        return {
          ...state,
          valueToAdd: action.payload,
        }
      default:
        return state;
    }
  };

  const CounterPage = ({ initialCount }) => {
    const increment = () => {
      dispatch({
        type: "increment"
      });
    };
    //...
    const handleChange = (e) => {
      const value = parseInt(e.target.value) || 0;
      dispatch({
        type: "change-value-to-add",
        payload: value
      });
    };
    //...
  };

  export default CounterPage;
  ```

## Const Action Types

String variables are prone to make typo mistakes.
- Create string variables with value of action types.
  - Same thing as writing the string out directly, but prevents typos.
- The ALL\_CAPS is another community convention.
  - Tells other engineers that this is an action type.
- ```jsx
  // "./pages/CounterPage.js"
  //...
  const INCREMENT_COUNT = "increment";
  const SET_VALUE_TO_ADD = "set-value-to-add";

  const reducer = (state, action) => {
    switch (action.type) {
      case INCREMENT_COUNT:
        //...
      case SET_VALUE_TO_ADD:
        //...
      default:
        return state;
    }
  };

  const CounterPage = ({ initialCount }) => {
    //...
    const increment = () => {
      // setCount(count + 1);
      dispatch({
        type: INCREMENT_COUNT
      });
    };
    //...
    const handleChange = (e) => {
      const value = parseInt(e.target.value) || 0;
      dispatch({
        type: SET_VALUE_TO_ADD,
        payload: value
      });
    };
    //...
  };

  export default CounterPage;
  ```

## Refactoring to Switch

In default case, some people throw an error.
- Running on default case is a sign of doing something that you did not expect.
- ```jsx
  // "./pages/CounterPage.js"
  //...
  const reducer = (state, action) => {
    //...
      default:
        throw new Error("Unexpected action type: " + action.type);
    };
  };
  //...
  export default CounterPage;
  ```

## Adding New State Updates

Follow three steps when you need a new way to update state.
1. Add a new constant action type string.
2. Add a call to `dispatch`.
3. Add a new case statement in your reducer.

- ```jsx
  // "./pages/CounterPage.js"
  //...
  const reducer = (state, action) => {
    switch (action.type) {
      //...
      case ADD_VALUE_TO_COUNT:
        return {
          ...state,
          count: state.count + state.valueToAdd,
          valueToAdd: 0
        }
      //...
    };
  };

  const CounterPage = ({ initialCount }) => {
    //...
    const handleSubmit = (e) => {
      e.preventDefault();
      dispatch({
        type: ADD_VALUE_TO_COUNT,
        payload: 0
      })
    };
    //...
  };

  export default CounterPage;
  ```

# A Few Design Considerations Around Reducers

## Purpose of `...state`

In `ADD_VALUE_TO_COUNT`, both `count` and `valueToAdd` variables are updated.
- Then what is the point of `...state`?
- Some times later, state object will be changed and additional feature will have been added.

## Desigh of Action Object

![Bad reducer design](https://i.imgur.com/YbHbu3o.png)

It is recommend to make a logic **in** the reducer function and keep the dispatches simple.
- Otherwise, you have to write a lot of logic at the location where we dispatch.
  - It is very easy to make mistakes.
- Less duplicated code if you need to dispatch the same action in multiple places.
- Part of the goal of reducers is to have a *very specific* set of ways that state can be changed.

# Introducing Immer

```jsx
case INCREMENT_COUNT:
  state.count = state.count + 1;
  return;
```
`Immer` library allows us to directly modify the state.
- You can directly mutate state.
  - Normally reducer must return a new object so that React rerenders components.
  - Directly changing state is not allowed.
- Do not have to return a new value.
- **Still needs `return` statement in each case, otherwise you get fallthrough**.

## Immer in Action

- Import `produce` from `immer`.
- Wrap `reducer` function with `provide`.
- ```jsx
  // "./pages/CounterPage.js"
  import produce from "immer";
  //...
  const reducer = (state, action) => {
    switch (action.type) {
      case INCREMENT_COUNT:
        state.count++;
        return;
      case DECREMENT_COUNT:
        state.count--;
        return;
      case SET_VALUE_TO_ADD:
        state.valueToAdd = action.payload;
        return;
      case ADD_VALUE_TO_COUNT:
        state.count += state.valueToAdd;
        state.valueToAdd = 0;
        return;
      default:
        throw new Error("Unexpected action type: " + action.type);
    };
  };

  const CounterPage = ({ initialCount }) => {
    const [state, dispatch] = useReducer(produce(reducer), {
      count: initialCount,
      valueToAdd: 0
    });
    //...
  };

  export default CounterPage;
  ```
