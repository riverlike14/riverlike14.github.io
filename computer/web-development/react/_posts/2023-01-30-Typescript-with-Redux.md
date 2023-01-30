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
