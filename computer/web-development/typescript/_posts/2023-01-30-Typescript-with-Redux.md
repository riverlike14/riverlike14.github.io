---
title: "[Typescript] Typescript with Redux"
---

# App Overview

![App overview](https://i.imgur.com/O0fAWnw.png)

![App overview2](https://imgur.com/CoS2aYP.png)

# Updated Install Command and Versions for React 18

Libraries
- `react-redux`
- `redux`
- `@types/react-redux`
- `redux-thunk@2.3.0`
- `axios@0.21.1`

# Project Setup

# Redux Store Design

In a normal Redux project, it is worth the time to think about the design of your store before writing code.

In a TS project, it is highly recommended to think about design first.

First, understand the data that we are going to fetch from that NPM api.

"registry.npmjs.org/-/v1/search?text=react"

Searching NPM

![Preview of the page](https://imgur.com/VtGO9DO.png)

1. We are fetching "packages" from NPM
2. "packages" is a reserved keyword in TS
3. We are going to call NPM packages "repositories"

![Redux store design](https://i.imgur.com/Ib4za4X.png)

![Redux store design with action](https://i.imgur.com/VAuQjhG.png)

![Bad directory structure](https://i.imgur.com/wFfZiP4.png)

![Recommend directory structure](https://i.imgur.com/b767xVr.png)

# Reducer Setup

Put redux-related files in the `./state` folder, and create a basic repository reducer function.
- ```tsx
  // "./state/reducers/repositoriesReducers.ts"
  interface RepositoriesState {
    loading: boolean,
    error: string | null;
    data: string[];
  }

  const reducer = (state: RepositoriesState, action: any) => {
    switch (action.type) {
      case "search_repositories":
        return { loading: true, error: null, data: [] };
      case "search_repositories_success":
        return { loading: false, error: null, data: action.payload };
      case "search_repositories_error":
        return { loading: false, error: action.payload, data: [] };
      default:
        return state;
    };
  };

  export default reducer;
  ```

## Annotating the Return Type

The code definitely works, but we are not using typescript properties enough.
- Typescript can see `action` and make sure the appropriate type of properties, instead of `any`.
  - Sometimes it might happen that `data` is an empty object.
  - When it happens, typescript should say something is not working correctly right now.
  - Typescript thus can prevent reducer from returning `data` of an empty object.
- ```tsx
  // "./state/reducers/repositoriesReducers.ts"
  interface RepositoriesState {
    loading: boolean,
    error: string | null;
    data: string[];
  }

  interface Action {
    type: string;
    payload?: any;
  }

  const reducer = (state: RepositoriesState, action: any): RepositoriesState => {
    switch (action.type) {
      case "search_repositories":
        return { loading: true, error: null, data: [] };
      case "search_repositories_success":
        return { loading: false, error: null, data: action.payload };
      case "search_repositories_error":
        return { loading: false, error: action.payload, data: [] };
      default:
        return state;
    };
  };

  export default reducer;
  ```
- But there is still `any` in `Action` interface: `payload?: any`.
  - We can improve further.


# Separate Interfaces for Actions

![Types for each different action](https://i.imgur.com/zvchnnV.png)

Create interfaces for each different action.

```tsx
// "./state/reducers/repositoriesReducers.ts"
//...

interface SearchRepositoriesAction {
  type: "search_repositories"
};

interface SearchRepositoriesSuccessAction {
    type: "search_repositories_success",
    payload: string[]
};

interface SearchRepositoriesErrorAction {
  type: "search_repositories_error",
  payload: string
};

//...

export default reducer;
```

# Applying Action Interfaces

Each `case` statement does [type guard](https://www.typescriptlang.org/docs/handbook/advanced-types.html).
Typescripts aware what kind of action it really is.

```tsx
// "./state/reducers/repositoriesReducers.ts"
interface RepositoriesState {
  loading: boolean,
  error: string | null;
  data: string[];
};

interface SearchRepositoriesAction {
  type: "search_repositories"
};

interface SearchRepositoriesSuccessAction {
    type: "search_repositories_success",
    payload: string[]
};

interface SearchRepositoriesErrorAction {
  type: "search_repositories_error",
  payload: string
};

const reducer = (
  state: RepositoriesState,
  action:
    SearchRepositoriesAction |
    SearchRepositoriesSuccessAction |
    SearchRepositoriesErrorAction
): RepositoriesState => {
  switch (action.type) {
    case "search_repositories":
      return { loading: true, error: null, data: [] };
    case "search_repositories_success":
      return { loading: false, error: null, data: action.payload };
    case "search_repositories_error":
      return { loading: false, error: action.payload, data: [] };
    default:
      return state;
  };
};

export default reducer;
```

# Applying Action Interfaces

Define Action type.

```tsx
// "./state/reducers/repositoriesReducers.ts"
interface RepositoriesState {
  loading: boolean,
  error: string | null;
  data: string[];
};

interface SearchRepositoriesAction {
  type: "search_repositories"
};

interface SearchRepositoriesSuccessAction {
    type: "search_repositories_success",
    payload: string[]
};

interface SearchRepositoriesErrorAction {
  type: "search_repositories_error",
  payload: string
};

type Action = 
    SearchRepositoriesAction |
    SearchRepositoriesSuccessAction |
    SearchRepositoriesErrorAction;

const reducer = (
  state: RepositoriesState,
  action: Action
): RepositoriesState => {
  switch (action.type) {
    case "search_repositories":
      return { loading: true, error: null, data: [] };
    case "search_repositories_success":
      return { loading: false, error: null, data: action.payload };
    case "search_repositories_error":
      return { loading: false, error: action.payload, data: [] };
    default:
      return state;
  };
};

export default reducer;
```

Create action type instead of string

```tsx
// "./state/reducers/repositoriesReducers.ts"
interface RepositoriesState {
  loading: boolean,
  error: string | null;
  data: string[];
};

interface SearchRepositoriesAction {
  type: ActionType.SEARCH_REPOSITORIES,
};

interface SearchRepositoriesSuccessAction {
    type: ActionType.SEARCH_REPOSITORIES_SUCCESS,
    payload: string[]
};

interface SearchRepositoriesErrorAction {
  type: ActionType.SEARCH_REPOSITORIES_ERROR,
  payload: string
};

enum ActionType {
  SEARCH_REPOSITORIES,
  SEARCH_REPOSITORIES_SUCCESS,
  SEARCH_REPOSITORIES_ERROR
};

type Action = 
    SearchRepositoriesAction |
    SearchRepositoriesSuccessAction |
    SearchRepositoriesErrorAction;

const reducer = (
  state: RepositoriesState,
  action: Action
): RepositoriesState => {
  switch (action.type) {
    case ActionType.SEARCH_REPOSITORIES:
      return { loading: true, error: null, data: [] };
    case ActionType.SEARCH_REPOSITORIES_SUCCESS:
      return { loading: false, error: null, data: action.payload };
    case ActionType.SEARCH_REPOSITORIES_ERROR:
      return { loading: false, error: action.payload, data: [] };
    default:
      return state;
  };
};

export default reducer;
```

# A Better Way to Organize Code

```tsx
// "./state/reducers/repositoriesReducers.ts"
import { ActionType } from "../action-types";
import { Action } from "../actions";

interface RepositoriesState {
  loading: boolean,
  error: string | null;
  data: string[];
};

const reducer = (
  state: RepositoriesState,
  action: Action
): RepositoriesState => {
  switch (action.type) {
    case ActionType.SEARCH_REPOSITORIES:
      return { loading: true, error: null, data: [] };
    case ActionType.SEARCH_REPOSITORIES_SUCCESS:
      return { loading: false, error: null, data: action.payload };
    case ActionType.SEARCH_REPOSITORIES_ERROR:
      return { loading: false, error: action.payload, data: [] };
    default:
      return state;
  };
};

export default reducer;
```

```tsx
// "./state/actions/index.ts"
import { ActionType } from "../action-types";

interface SearchRepositoriesAction {
  type: ActionType.SEARCH_REPOSITORIES,
};

interface SearchRepositoriesSuccessAction {
    type: ActionType.SEARCH_REPOSITORIES_SUCCESS,
    payload: string[]
};

interface SearchRepositoriesErrorAction {
  type: ActionType.SEARCH_REPOSITORIES_ERROR,
  payload: string
};

export type Action = 
    SearchRepositoriesAction |
    SearchRepositoriesSuccessAction |
    SearchRepositoriesErrorAction;
```

```tsx
// "./state/action-types/index.ts"
export enum ActionType {
  SEARCH_REPOSITORIES,
  SEARCH_REPOSITORIES_SUCCESS,
  SEARCH_REPOSITORIES_ERROR
};
```

There can be many more different kinds of action types and actions

# Adding Action Creators

Making an action creator that will make a request to the npm api.

create folder `/state/action-creators` and make `/state/action-creators/index.ts` file.

```ts
// "./state/action-creators/index.ts"
import axios from "axios";
import { ActionType } from "../action-types";
import { Action } from "../actions";

const searchRepositories = (term: string) => {
  return async (dispatch: any) => {
    dispatch({
      type: ActionType.SEARCH_REPOSITORIES
    });

    try {
      ;
    } catch (err: any) {
      dispatch({
        type: ActionType.SEARCH_REPOSITORIES_ERROR,
        payload: err.message
      });
    }
  }
};
```

# Adding Request Logic

```ts
// "./state/action-creators/index.ts"
import axios from "axios";
import { ActionType } from "../action-types";
import { Action } from "../actions";

export const searchRepositories = (term: string) => {
  return async (dispatch: any) => {
    dispatch({
      type: ActionType.SEARCH_REPOSITORIES
    });

    try {
      const { data } = await axios.get(
        "https://registry.npmjs.org/-/v1/search", {
        params: {
          text: term
        }
      });

      const names = data.objects.map((result: any) => {
        return result.package.name;
      });

      dispatch({
        type: ActionType.SEARCH_REPOSITORIES_SUCCESS,
        payload: names,
      });

    } catch (err: any) {
      dispatch({
        type: ActionType.SEARCH_REPOSITORIES_ERROR,
        payload: err.message
      });
    }
  }
};
```

# Applying Typings to Dispatch

Some possible improvements.
payload of `SEARCH_REPOSITORIES_SUCCESS` must be an array of strings.
- Get typescript understand how we are calling the dispatch function and do some kind of checking that we are providing an appropriate kind of action.
- `import { Dispatch } from "redux"`
  - type definition of dispatch function.
  - `(dispatch: Dispatch<Action>)`
  - It tells typescript that we are going to get a function that can only be called with some argument that matches this type of action.

```ts
// "./state/action-creators/index.ts"
import axios from "axios";
import { Dispatch } from "redux";
import { ActionType } from "../action-types";
import { Action } from "../actions";

export const searchRepositories = (term: string) => {
  return async (dispatch: Dispatch<Action>) => {
    dispatch({
      type: ActionType.SEARCH_REPOSITORIES
    });

    try {
      const { data } = await axios.get(
        "https://registry.npmjs.org/-/v1/search", {
        params: {
          text: term
        }
      });

      const names = data.objects.map((result: any) => {
        return result.package.name;
      });

      dispatch({
        type: ActionType.SEARCH_REPOSITORIES_SUCCESS,
        payload: names,
      });

    } catch (err: any) {
      dispatch({
        type: ActionType.SEARCH_REPOSITORIES_ERROR,
        payload: err.message
      });
    }
  }
};
```

# Setting Up Exports

Create an actual redux store

```ts
// "./state/reducers/index.ts"
import { combineReducers } from "redux";
import repositoriesReducer from "./repositoriesReducer";

const reducers = combineReducers({
  repositories: repositoriesReducer
});

export default reducers;
```

```ts
// "./state/store.ts"
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk";
import reducers from "./reducers";

export const store = createStore(reducers, {}, applyMiddleware(thunk));
```

If we have central `index.ts` inside root-redux folder(in our case, state folder), we can then import everything from action creators, middle-wares, into this `index.ts` file and then export them from there.

```ts
// "./state/index.ts"
export * from "./store";
export * as actionCreators from "./action-creators";
```

Finished up redux side of project.

# Wiring Up to React

Start to connect redux side to react side of things.

```tsx
// "./components/App.tsx"
import { Provider } from "react-redux";
import { store } from "../state";
import RepositoriesList from "./RepositoriesList";

const App = () => {
  return (
    <Provider store={store}>
      <h1>Search For a Package</h1>
      <RepositoriesList />
    </Provider>
  );
}

export default App;
```

```tsx
// "./components/RepositoriesList.tsx"
const RepositoriesList: React.FC = () => {
  return (
    <div>
      <form>
        <input />
        <button>Search</button>
      </form>
    </div>
  );
};

export default RepositoriesList;
```

Error: We need to add in an initial state.

# Oops... Initial State!

Initialize state argument.

```ts
// "./state/reducers/repositoriesReducers.ts"
import { ActionType } from "../action-types";
import { Action } from "../actions";

interface RepositoriesState {
  loading: boolean,
  error: string | null;
  data: string[];
};

const initialState = {
  loading: false,
  error: null,
  data: []
};

const reducer = (
  state: RepositoriesState = initialState,
  action: Action
): RepositoriesState => {
  switch (action.type) {
    case ActionType.SEARCH_REPOSITORIES:
      return { loading: true, error: null, data: [] };
    case ActionType.SEARCH_REPOSITORIES_SUCCESS:
      return { loading: false, error: null, data: action.payload };
    case ActionType.SEARCH_REPOSITORIES_ERROR:
      return { loading: false, error: action.payload, data: [] };
    default:
      return state;
  };
};

export default reducer;
```

![The app is working](https://imgur.com/aHxYtZq.png)

# Reminder on Event Types

```tsx
// "./components/RepositoriesList.tsx"
import { useState } from "react";

const RepositoriesList: React.FC = () => {
  const [term, setTerm] = useState("");

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
  };

  return (
    <div>
      <form onSubmit={onSubmit} >
        <input value={term} onChange={e => setTerm(e.target.value)}/>
        <button>Search</button>
      </form>
    </div>
  );
};

export default RepositoriesList;
```

# Calling an Action Creator

```tsx
// "./components/RepositoriesList.tsx"
import { useState } from "react";
import { useDispatch } from "react-redux";
import { actionCreators } from "../state";

const RepositoriesList: React.FC = () => {
  const [term, setTerm] = useState("");
  const dispatch = useDispatch();

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    dispatch(actionCreators.searchRepositories(term) as any);
  };

  return (
    <div>
      <form onSubmit={onSubmit} >
        <input value={term} onChange={e => setTerm(e.target.value)}/>
        <button>Search</button>
      </form>
    </div>
  );
};

export default RepositoriesList;
```

`dispatch(actionCreators.searchRepositories(term) as any);`
- Will work, but too long...

# Binding Action Creators

We are going to create a hook that is automatically give us access to all of different action creators inside our components.

create `./hooks/useActions.ts`

```ts
// "./hooks/useActions.ts"
import { useDispatch } from "react-redux";
import { bindActionCreators } from "redux";
import { actionCreators } from "../state";

export const useActions = () => {
  const dispatch = useDispatch();

  return bindActionCreators(actionCreators, dispatch);
};
```

```tsx
// "./components/RepositoriesList.tsx"
import { useState } from "react";
import { useActions } from "../hooks/useActions";

const RepositoriesList: React.FC = () => {
  const [term, setTerm] = useState("");
  const { searchRepositories } = useActions();

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    searchRepositories(term);
  };

  return (
    <div>
      <form onSubmit={onSubmit} >
        <input value={term} onChange={e => setTerm(e.target.value)}/>
        <button>Search</button>
      </form>
    </div>
  );
};

export default RepositoriesList;
```

# Selecting State

We need to get access to some state that is stored inside of RepositoriesList.

```tsx
// "./components/RepositoriesList.tsx"
import { useState } from "react";
import { useSelector } from "react-redux";
import { useActions } from "../hooks/useActions";

const RepositoriesList: React.FC = () => {
  const [term, setTerm] = useState("");
  const { searchRepositories } = useActions();
  const state = useSelector((state) => state);
  console.log(state);

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    searchRepositories(term);
  };

  return (
    <div>
      <form onSubmit={onSubmit} >
        <input value={term} onChange={e => setTerm(e.target.value)}/>
        <button>Search</button>
      </form>
    </div>
  );
};

export default RepositoriesList;
```
console.log(state);

Only need loading, error, and data.

```tsx
// "./components/RepositoriesList.tsx"
import { useState } from "react";
import { useSelector } from "react-redux";
import { useActions } from "../hooks/useActions";

const RepositoriesList: React.FC = () => {
  const [term, setTerm] = useState("");
  const { searchRepositories } = useActions();
  const { data, error, loading } = useSelector((state) => state.repositories);

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    searchRepositories(term);
  };

  return (
    <div>
      <form onSubmit={onSubmit} >
        <input value={term} onChange={e => setTerm(e.target.value)}/>
        <button>Search</button>
      </form>
    </div>
  );
};

export default RepositoriesList;
```

- Property `repositories` does not exist on type `DefaultRootState`...
- useSelector has no idea what type of data is inside of redux store.
- No information is being communicated from redux-side over to react-redux-side.

# Awkward Typing Around React-Redux

- Figure out what the type of our data is inside of our redux store
- And then communicate that informatino to react-redux

What we have to do.
Step 1. Create a new type that describes the type of data inside of our redux store

```ts
// "./state/reducers/index.ts"
import { combineReducers } from "redux";
import repositoriesReducer from "./repositoriesReducer";

const reducers = combineReducers({
  repositories: repositoriesReducer
});

export default reducers;

export type RootState = ReturnType<typeof reducers>;
```

- Meaning: We are creating some reducers, and this is going to be a function. 
- `ReturnType`: Take that function, and give us back the type whatever that function returns

```ts
// "./state/index.ts"
export * from "./store";
export * as actionCreators from "./action-creators";
export * from "./reducers";
```

# Creating a Typed Selector

Step 2.

```ts
import { useSelector, TypedUseSelectorHook } from "react-redux";
import { RootState } from "../state";

export const useTypedSelector: TypedUseSelectorHook<RootState> = useSelector;
```
- This is from the documentation...

```tsx
// "./components/RepositoriesList.tsx"
import { useState } from "react";
import { useTypedSelector } from "../hooks/useTypedSelector";
import { useActions } from "../hooks/useActions";

const RepositoriesList: React.FC = () => {
  const [term, setTerm] = useState("");
  const { searchRepositories } = useActions();
  const { data, error, loading } = useTypedSelector((state) => state.repositories);

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    searchRepositories(term);
  };

  return (
    <div>
      <form onSubmit={onSubmit} >
        <input value={term} onChange={e => setTerm(e.target.value)}/>
        <button>Search</button>
      </form>
    </div>
  );
};

export default RepositoriesList;
```

# Consuming Store State

```tsx
// "./components/RepositoriesList.tsx"
import { useState } from "react";
import { useTypedSelector } from "../hooks/useTypedSelector";
import { useActions } from "../hooks/useActions";

const RepositoriesList: React.FC = () => {
  const [term, setTerm] = useState("");
  const { searchRepositories } = useActions();
  const { data, error, loading } = useTypedSelector((state) => state.repositories);

  const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    searchRepositories(term);
  };

  return (
    <div>
      <form onSubmit={onSubmit} >
        <input value={term} onChange={e => setTerm(e.target.value)}/>
        <button>Search</button>
      </form>
      { error && <h3>{error}</h3> }
      { loading && <h3>Loading...</h3> }
      { !error && !loading &&
        data.map(name => <div key={name}>{name}</div>)
      }
    </div>
  );
};

export default RepositoriesList;
```

# Quick Wrapup

Big Issues with Redux/React-Redux + Typescript
- Imports between files can turn into a mess very quickly
- Communicating types over to your components can be challenging
- Type def files for Redux, React-Redux, and others are *possibly* over-engineered
