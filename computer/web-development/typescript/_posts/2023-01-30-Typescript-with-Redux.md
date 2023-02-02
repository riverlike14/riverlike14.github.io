---
title: "[Typescript] Typescript with Redux"
---

# App Overview

![App overview](https://i.imgur.com/O0fAWnw.png)

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

Put redux-related files in the `./state` folder.

```tsx
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

- It works.
- Not using typescript properties enough
  - Sometimes it might happen that `data: {}`
  - Want typescript to say you are not kinda doing something correctly right now.
  - Typescript should see action and make sure the appropriate type of properties.
    - not to returning something like `data: {}` or something like that.

# Annotating the Return Type

Return type annotation.

Set return type as `RepositoriesState`, by adding colon, RepositoriesState.

```tsx
// "./state/reducers/repositoriesReducers.ts"
interface RepositoriesState {
	loading: boolean,
	error: string | null;
	data: string[];
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

# Typing an Action

```tsx
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

const reducer = (state: RepositoriesState, action: Action): RepositoriesState => {
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

- Still has `any` in `payload?: any`. We can improve further.


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
