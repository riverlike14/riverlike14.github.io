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

understanding how we update our state is challenging.

State Updates with `useReducer`

![Flow of redux](https://i.imgur.com/7p7RXXh.png)

When you call `dispatch`, react is gonna go and find `reducer` function and run it.
- The first argument is current state that is being maintained by reducer
- The second argument is action, you can pass whatever you want to pass to dispatch
- More than one element in dispatch is going to be ignored
- The argument passed to dispatch is going to show up in reducer as the second argument, action.
- Whatever the reducer returns is the new state


Rules around Reducer Functions
- Whatever you return will be your new state
- If you return nothing, then your state will be *`undefined`!*
- **No async/await, no requests, no promises, no outside variables**
- **Like almost everywhere else in React, don't directly modify the state object!**

Sample of only increment action
```jsx
// "./pages/CounterPage.js"
import { useState, useReducer } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const reducer = (state, action) => {
  return {
    ...state,
    count: state.count + 1
  }
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
    dispatch();
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

# Understanding Action Objects

We need some way of telling the reducer what should it do to update the state
- When we call `dispatch()`, we need to pass along soe info to tell the reducer *how the state should be updated*
- Billion ways to do this!
- The React community has come up with a convention on how to tell the reducer what it needs to do

![Community solution of redux desigh](https://i.imgur.com/bXPH71h.png)
- **When we need to modify state, we will call dispatch and *always* pass in an "action" object**
- The "action" object will *always* have a **"type"** property that is a string. This helps tell the reducer what state update it needs to make
- If we need to communicate some data to the reducer, it will be placed on the **"payload"** property of the action object
- **This is a very common commounity convention, not a requirement. React doesn't treat these action objects any differently.**

```jsx
// "./pages/CounterPage.js"
import { useState, useReducer } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

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
  // const [count, setCount] = useState(initialCount);
  // const [valueToAdd, setValueToAdd] = useState(0);
  const [state, dispatch] = useReducer(reducer, {
    count: initialCount,
    valueToAdd: 0
  });

  const increment = () => {
    // setCount(count + 1);
    dispatch({
      type: "increment"
    });
  };

  const decrement = () => {
    // setCount(count - 1);
  };

  const handleChange = (e) => {
    const value = parseInt(e.target.value) || 0;
    dispatch({
      type: "change-value-to-add",
      payload: value
    });
    // setValueToAdd(value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // setCount(count + valueToAdd);
    // setValueToAdd(0);
  };

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

# Const Action Types

Action types are string. 

It is very easy to make typos...
- Same thing as writing the string out directly, just prevents typos
- If you make a typo in the variable name, an error will be thrown
- The `ALL_CAPS` is another community convention. Tells other engineers that this is an action type

```jsx
// "./pages/CounterPage.js"
import { useState, useReducer } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const INCREMENT_COUNT = "increment";
const SET_VALUE_TO_ADD = "set-value-to-add";

const reducer = (state, action) => {
  switch (action.type) {
    case INCREMENT_COUNT:
      return {
        ...state,
        count: state.count + 1
      };
    case SET_VALUE_TO_ADD:
      return {
        ...state,
        valueToAdd: action.payload,
      }
    default:
      return state;
  }
};

const CounterPage = ({ initialCount }) => {
  // const [count, setCount] = useState(initialCount);
  // const [valueToAdd, setValueToAdd] = useState(0);
  const [state, dispatch] = useReducer(reducer, {
    count: initialCount,
    valueToAdd: 0
  });

  const increment = () => {
    // setCount(count + 1);
    dispatch({
      type: INCREMENT_COUNT
    });
  };

  const decrement = () => {
    // setCount(count - 1);
  };

  const handleChange = (e) => {
    const value = parseInt(e.target.value) || 0;
    dispatch({
      type: SET_VALUE_TO_ADD,
      payload: value
    });
    // setValueToAdd(value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // setCount(count + valueToAdd);
    // setValueToAdd(0);
  };

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

# Refactoring to Switch

In default case, some people throw an error.
The program is doing something that you did not expect.

```jsx
// "./pages/CounterPage.js"
import { useState, useReducer } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const INCREMENT_COUNT = "increment";
const SET_VALUE_TO_ADD = "set-value-to-add";

const reducer = (state, action) => {
  switch (action.type) {
    case INCREMENT_COUNT:
      return {
        ...state,
        count: state.count + 1
      };
    case SET_VALUE_TO_ADD:
      return {
        ...state,
        valueToAdd: action.payload,
      }
    default:
      throw new Error("Unexpected action type: " + action.type);
  };
};

const CounterPage = ({ initialCount }) => {
  // const [count, setCount] = useState(initialCount);
  // const [valueToAdd, setValueToAdd] = useState(0);
  const [state, dispatch] = useReducer(reducer, {
    count: initialCount,
    valueToAdd: 0
  });

  const increment = () => {
    // setCount(count + 1);
    dispatch({
      type: INCREMENT_COUNT
    });
  };

  const decrement = () => {
    // setCount(count - 1);
  };

  const handleChange = (e) => {
    const value = parseInt(e.target.value) || 0;
    dispatch({
      type: SET_VALUE_TO_ADD,
      payload: value
    });
    // setValueToAdd(value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // setCount(count + valueToAdd);
    // setValueToAdd(0);
  };

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

# Adding New State Updates

I need to update my state in a new way!
1. Add a new constant action type string
2. Add a call to `dispatch`
3. Add a new case statement in your reducer

```jsx
// "./pages/CounterPage.js"
import { useState, useReducer } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const INCREMENT_COUNT = "increment";
const DECREMENT_COUNT = "decrement";
const SET_VALUE_TO_ADD = "change-value-to-add";
const ADD_VALUE_TO_COUNT = "add-value-to-count";

const reducer = (state, action) => {
  switch (action.type) {
    case INCREMENT_COUNT:
      return {
        ...state,
        count: state.count + 1
      };
    case DECREMENT_COUNT:
      return {
        ...state,
        count: state.count - 1
      }
    case SET_VALUE_TO_ADD:
      return {
        ...state,
        valueToAdd: action.payload,
      }
    case ADD_VALUE_TO_COUNT:
      return {
        ...state,
        count: state.count + state.valueToAdd,
        valueToAdd: 0
      }
    default:
      throw new Error("Unexpected action type: " + action.type);
  };
};

const CounterPage = ({ initialCount }) => {
  // const [count, setCount] = useState(initialCount);
  // const [valueToAdd, setValueToAdd] = useState(0);
  const [state, dispatch] = useReducer(reducer, {
    count: initialCount,
    valueToAdd: 0
  });

  const increment = () => {
    // setCount(count + 1);
    dispatch({
      type: INCREMENT_COUNT
    });
  };

  const decrement = () => {
    dispatch({
      type: DECREMENT_COUNT
    })
    // setCount(count - 1);
  };

  const handleChange = (e) => {
    const value = parseInt(e.target.value) || 0;
    dispatch({
      type: SET_VALUE_TO_ADD,
      payload: value
    });
    // setValueToAdd(value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    dispatch({
      type: ADD_VALUE_TO_COUNT,
      payload: 0
    })
    // setCount(count + valueToAdd);
    // setValueToAdd(0);
  };

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

# A Few Design Considerations Around Reducers

```jsx
const reducer = (state, action) => {
  switch (action.type) {
    case INCREMENT_COUNT:
      return {
        ...state,
        count: state.count + 1
      };
    case DECREMENT_COUNT:
      return {
        ...state,
        count: state.count - 1
      }
    case SET_VALUE_TO_ADD:
      return {
        ...state,
        valueToAdd: action.payload,
      }
    case ADD_VALUE_TO_COUNT:
      return {
        ...state,
        count: state.count + state.valueToAdd,
        valueToAdd: 0
      }
    default:
      throw new Error("Unexpected action type: " + action.type);
  };
};
```

In `ADD_VALUE_TO_COUNT`, what is the point of `...state`?
- Some times later, state object has changed and additional feature has been added.

![Bad reducer design](https://i.imgur.com/YbHbu3o.png)
Lot of logic at the location where we do the dispatch
- very easy to make typos

- *Usually* makes more sense to stuff logic into the reducer and keep the dispatches simple
- Less duplicated code if you need to dispatch the same action in multiple places
- Part of the goal of reducers is to have a *very specific* set of ways that state can be changed

# Introducing Immer

```jsx
// ...
case INCREMENT_COUNT:
  state.count = state.count + 1;
  return;
```

- Immer library allows us to directly modify the state


Normal Reducer
- No directly changing state
- Must return a new value to use for state

Reducer with Immer
- You can mutate state
- Do not have to return a new value
- **Stil return in each case, otherwise you get "failthrough"**

Install Immer
`npm install immer`

# Immer in Action

`import produce from "immer";`

`const [state, dispatch] = useReducer(produce(reduce), ...)`

```jsx
// "./pages/CounterPage.js"
import produce from "immer";
import { useState, useReducer } from "react";
import Button from "../components/Button";
import Panel from "../components/Panel";

const INCREMENT_COUNT = "increment";
const DECREMENT_COUNT = "decrement";
const SET_VALUE_TO_ADD = "change-value-to-add";
const ADD_VALUE_TO_COUNT = "add-value-to-count";

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
  // const [count, setCount] = useState(initialCount);
  // const [valueToAdd, setValueToAdd] = useState(0);
  const [state, dispatch] = useReducer(produce(reducer), {
    count: initialCount,
    valueToAdd: 0
  });

  const increment = () => {
    // setCount(count + 1);
    dispatch({
      type: INCREMENT_COUNT
    });
  };

  const decrement = () => {
    dispatch({
      type: DECREMENT_COUNT
    })
    // setCount(count - 1);
  };

  const handleChange = (e) => {
    const value = parseInt(e.target.value) || 0;
    dispatch({
      type: SET_VALUE_TO_ADD,
      payload: value
    });
    // setValueToAdd(value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    dispatch({
      type: ADD_VALUE_TO_COUNT,
      payload: 0
    })
    // setCount(count + valueToAdd);
    // setValueToAdd(0);
  };

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
