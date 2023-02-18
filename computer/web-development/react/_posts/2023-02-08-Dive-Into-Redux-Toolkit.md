---
title: "[React] Dive Into Redux Toolkit"
---

# Into the World of Redux

Redux is a library for managing state using the *same techniques* as useReducer

![useReducer and Redux are similar](https://i.imgur.com/H6i2pPa.png)

- With `useReducer`, all of our state was created and maintained in the React world
- With Redux, we create a `store` to create and maintain our state
  - Individual components can connect to the store and access state

![React-Redux](https://i.imgur.com/gLxZrWV.png)

- The Redux library does not assume you are using React!
- React-Redux is a library to help communicate between the React and Redux sides of your project

In useReducer
- One single reducer function to manage state

In Redux
- Multiple reducers, each managing a different part of state
- State object is end up with a little bit more complicated

# Redux vs Redux Toolkit

![Popular state managing pattern](https://i.imgur.com/Ku4tunx.png)

Why is this pattern so popular?

If we want to change state in *any way*, we must call dispatch!
- This is good and bad
- The dispatch function is the central point of initiating *any change of state*
- By simply adding in console.log, it becomes easy to understand why state is changing!
- However, before we came up with the idea of dispatching an action object
- All this extra code just to communicate to the reducer how state is supposed to change
- We have to write out a lot of boilerplate code just to make sure the reducer knows what to do!

![Why use Redux Toolkit](https://i.imgur.com/KpF1d0A.png)

Option #1: Use 'classic' Redux
- This is the older style

Option #2: Use Redux Toolkit(RTK)
- RTK is a wrapper around plain Redux
- ***Specifically simplifies the action type creation process!***
- This is the recommended way moving forward

# App Overview

![App Design](https://i.imgur.com/jHs3rbM.png)

## Libraries

- faker-js
- bulma
- redux toolkit

# The Path Forward

![Redux Store design](https://i.imgur.com/AXq6H1h.png)

Boilerplate codes

```jsx
// "./App.js"
import "./styles.css";
import MoviePlaylist from "./components/MoviePlaylist";
import SongPlaylist from "./components/SongPlaylist";

export default function App() {
  const handleResetClick = () => {
    //
  };

  return (
    <div className="container is-fluid">
      <button onClick={() => handleResetClick()} className="button is-danger">
        Reset Both Playlists
      </button>
      <hr />
      <MoviePlaylist />
      <hr />
      <SongPlaylist />
    </div>
  );
}
```

```jsx
// "./components/MoviePlaylist.js"
import { createRandomMovie } from "../data";

function MoviePlaylist() {
  // To Do:
  // Get list of movies
  const moviePlaylist = [];

  const handleMovieAdd = (movie) => {
    // To Do:
    // Add movie to list of movies
  };
  const handleMovieRemove = (movie) => {
    // To Do:
    // Remove movie from list of movies
  };

  const renderedMovies = moviePlaylist.map((movie) => {
    return (
      <li key={movie}>
        {movie}
        <button
          onClick={() => handleMovieRemove(movie)}
          className="button is-danger"
        >
          X
        </button>
      </li>
    );
  });

  return (
    <div className="content">
      <div className="table-header">
        <h3 className="subtitle is-3">Movie Playlist</h3>
        <div className="buttons">
          <button
            onClick={() => handleMovieAdd(createRandomMovie())}
            className="button is-link"
          >
            + Add Movie to Playlist
          </button>
        </div>
      </div>
      <ul>{renderedMovies}</ul>
    </div>
  );
}

export default MoviePlaylist;
```

```jsx
// "./components/SongPlaylist.js"
import { createRandomSong } from "../data";

function SongPlaylist() {
  // To Do:
  // Get list of songs
  const songPlaylist = [];

  const handleSongAdd = (song) => {
    // To Do:
    // Add song to list of songs
  };
  const handleSongRemove = (song) => {
    // To Do:
    // Remove song from list of songs
  };

  const renderedSongs = songPlaylist.map((song) => {
    return (
      <li key={song}>
        {song}
        <button
          onClick={() => handleSongRemove(song)}
          className="button is-danger"
        >
          X
        </button>
      </li>
    );
  });

  return (
    <div className="content">
      <div className="table-header">
        <h3 className="subtitle is-3">Song Playlist</h3>
        <div className="buttons">
          <button
            onClick={() => handleSongAdd(createRandomSong())}
            className="button is-link"
          >
            + Add Song to Playlist
          </button>
        </div>
      </div>
      <ul>{renderedSongs}</ul>
    </div>
  );
}

export default SongPlaylist;
```

```js
// "./data/index.js"
import { faker } from "@faker-js/faker/locale/en";

// This file has nothing to do with Redux
// It exports functions that create random
// movies and song

export const createRandomMovie = () => {
  return `${faker.word.adjective()} ${faker.word.noun()}`;
};

export const createRandomSong = () => {
  return faker.music.songName();
};
```

# Implementation Time!

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      //
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer
  }
});

console.log(store);
```

# Understanding the Store

- The store is an object that will hold all of our state
- We ***usually*** do not have to interact with it directly. The 'React-Redux' library will handle the store for us
- **To dispatch an action**:
  - `store.dispatch({ type: "songs/addSong" })`
- **To see what state exists in the store**:
  - `store.getState()`

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      //
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer
  }
});

const startingState = store.getState();
console.log(startingState);

store.dispatch({
  type: "song/addSong",
  payload: "New Song!"
});

const finalState = store.getState();
console.log(finalState);
```

In Redux store,
- Keys(songs, movies, ...) for the big object are set when the store is created
- Values([song1, ...], [movie1, ...], ...) are produced by the individual reducers
- Remember, it is usually nice to store all state in a single big object

# The Store's Initial State

Slices automatically create reducers and action types
- Slice defines some initial state
- Slice combines 'mini-reducers' into a big reducer
- Slice creates a set of 'action creator' functions

Slice defines some initial state
- Store startup time! Need some initial state!
  - Get the slice's 'initialState' property

# Understanding Slices

![Slices save us from writing boilerplates](https://i.imgur.com/2ot0Ys1.png)

'reducers' object in 'songsSlice' &rarr; songsSlice &rarr; Combined reducer created by songsSlice

# Understanding Action Creators

Action creators
- Set of functions created for us automatically
- When called, they return an action that we can dispatch
- Saves us from having to manually write out action objects
  - This is the only goal...

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      //
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer
  }
});

console.log(songsSlice.actions.addSong());

/*
const startingState = store.getState();
console.log(startingState);

store.dispatch({
  type: "song/addSong",
  payload: "New Song!"
});

const finalState = store.getState();
console.log(finalState);
*/
```

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      //
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer
  }
});

// console.log(songsSlice.actions.addSong());

const startingState = store.getState();
console.log(startingState);

store.dispatch(
  songsSlice.actions.addSong("Some Song!")
);

const finalState = store.getState();
console.log(finalState);
```

# Connecting React to Redux

![Redux slice design](https://i.imgur.com/Rx096K6.png)

Connecting React to Redux
- Once per project
1. Export the `store` from whatever file it is created in
2. Import the state into the root `index.js` file
3. Import `Provider` from the "react-redux" library
4. Wrap the App component with the Provider, pass the store to the Provider

```jsx
// "./index.js"
import "bulma/css/bulma.css";
import { createRoot } from "react-dom/client";
import { Provider } from "react-redux";
import App from "./App";
import { store } from "./store";

const rootElement = document.getElementById("root");
const root = createRoot(rootElement);

root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

# Updating State from a Component

Changing State
- Many times per project!
1. Add a reducer to one of your slices that changes state in some particular way
2. Export the action creator that the slice automatically creates
3. Find the component that you want to dispatch from
4. Import the action creator function and `useDispatch` from "react-redux"
5. Call the `useDispatch` hook to get access to the dispatch function
6. When the user does something, call the action creator to get an action, then dispatch it

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      //
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer
  }
});

export { store };
export const { addSong } = songsSlice.actions;
```

```jsx
// "./components/SongPlaylist.js"
import { useDispatch } from "react-redux";
import { createRandomSong } from "../data";
import { addSong } from "../store"

function SongPlaylist() {
  const dispatch = useDispatch();
  // To Do:
  // Get list of songs
  const songPlaylist = [];

  const handleSongAdd = (song) => {
    dispatch(addSong(song));
  };

  const handleSongRemove = (song) => {
    // To Do:
    // Remove song from list of songs
  };

  const renderedSongs = songPlaylist.map((song) => {
    return (
      <li key={song}>
        {song}
        <button
          onClick={() => handleSongRemove(song)}
          className="button is-danger"
        >
          X
        </button>
      </li>
    );
  });

  return (
    <div className="content">
      <div className="table-header">
        <h3 className="subtitle is-3">Song Playlist</h3>
        <div className="buttons">
          <button
            onClick={() => handleSongAdd(createRandomSong())}
            className="button is-link"
          >
            + Add Song to Playlist
          </button>
        </div>
      </div>
      <ul>{renderedSongs}</ul>
    </div>
  );
}

export default SongPlaylist;
```

# Accessing State in a Component
- Many times per project!
1. Find the component that needs to access some state
2. Import the `useSelector` hook from "react-redux"
3. Call the hook, passing in a selector function
4. Use the state! Anytime state changes, the component will automatically rerender

# Removing Content

step 1, 2
```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) { // step 1
      const idx = state.indexOf(action.payload);
      state.splice(idx, 1);
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer
  }
});

export { store };
export const { addSong, removeSong } = songsSlice.actions; // step 2
```

step 3 skip

step 4
step 5 skip 
step 6

```jsx
// "./components/SongPlaylist.js"
import { useDispatch, useSelector } from "react-redux";
import { createRandomSong } from "../data";
import { addSong, removeSong } from "../store" // step 4

function SongPlaylist() {
  const dispatch = useDispatch();

  const songPlaylist = useSelector((state) => {
    return state.songs;
  });

  const handleSongAdd = (song) => {
    dispatch(addSong(song));
  };

  const handleSongRemove = (song) => {
    dispatch(removeSong(song)); // step 6
  };

  const renderedSongs = songPlaylist.map((song) => {
    return (
      <li key={song}>
        {song}
        <button
          onClick={() => handleSongRemove(song)}
          className="button is-danger"
        >
          X
        </button>
      </li>
    );
  });

  return (
    <div className="content">
      <div className="table-header">
        <h3 className="subtitle is-3">Song Playlist</h3>
        <div className="buttons">
          <button
            onClick={() => handleSongAdd(createRandomSong())}
            className="button is-link"
          >
            + Add Song to Playlist
          </button>
        </div>
      </div>
      <ul>{renderedSongs}</ul>
    </div>
  );
}

export default SongPlaylist;
```

# Practice Updating State!

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const moviesSlice = createSlice({
  name: "movie",
  initialState: [],
  reducers: {
    addMovie(state, action) {
      state.push(action.payload);
    },
    removeMovie(state, action) {
      const idx = state.indexOf(action.payload);
      state.splice(idx, 1);
    }
  }
});

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      const idx = state.indexOf(action.payload);
      state.splice(idx, 1);
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer,
    movies: moviesSlice.reducer
  }
});

export { store };
export const { addSong, removeSong } = songsSlice.actions;
export const { addMovie, removeMovie } = moviesSlice.actions;
```

```jsx
// "./components/MoviePlaylist.js"
import { useDispatch } from "react-redux";
import { createRandomMovie } from "../data";
import { addMovie, removeMovie } from "../store";

function MoviePlaylist() {
  const dispatch = useDispatch();

  const moviePlaylist = [];

  const handleMovieAdd = (movie) => {
    dispatch(addMovie(movie));
  };

  const handleMovieRemove = (movie) => {
    dispatch(removeMovie(movie));
  };

  const renderedMovies = moviePlaylist.map((movie) => {
    return (
      <li key={movie}>
        {movie}
        <button
          onClick={() => handleMovieRemove(movie)}
          className="button is-danger"
        >
          X
        </button>
      </li>
    );
  });

  return (
    <div className="content">
      <div className="table-header">
        <h3 className="subtitle is-3">Movie Playlist</h3>
        <div className="buttons">
          <button
            onClick={() => handleMovieAdd(createRandomMovie())}
            className="button is-link"
          >
            + Add Movie to Playlist
          </button>
        </div>
      </div>
      <ul>{renderedMovies}</ul>
    </div>
  );
}

export default MoviePlaylist;
```

# Practice Accessing State!

```jsx
// "./components/MoviePlaylist.js"
import { useDispatch, useSelector } from "react-redux";
//...
function MoviePlaylist() {
  //...
  const moviePlaylist = useSelector(state => {
    return state.movies;
  })
  //...
}

export default MoviePlaylist;
```

# Resetting State

State update is little bit different.
First, empty out movie playlist

By immer, `state = []` does not work.
`return [];` works

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const moviesSlice = createSlice({
  name: "movie",
  initialState: [],
  reducers: {
    addMovie(state, action) {
      state.push(action.payload);
    },
    removeMovie(state, action) {
      const idx = state.indexOf(action.payload);
      state.splice(idx, 1);
    },
    reset(state, action) {
      return [];
    }
  }
});

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      const idx = state.indexOf(action.payload);
      state.splice(idx, 1);
    }
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer,
    movies: moviesSlice.reducer
  }
});

export { store };
export const { addSong, removeSong } = songsSlice.actions;
export const { addMovie, removeMovie, reset } = moviesSlice.actions;
```

```jsx
// "./App.js"
import "./styles.css";
import { useDispatch } from "react-redux";
import MoviePlaylist from "./components/MoviePlaylist";
import SongPlaylist from "./components/SongPlaylist";
import { reset } from "./store";

export default function App() {
  const dispatch = useDispatch();

  const handleResetClick = () => {
    dispatch(reset());
  };

  return (
    <div className="container is-fluid">
      <button onClick={() => handleResetClick()} className="button is-danger">
        Reset Both Playlists
      </button>
      <hr />
      <MoviePlaylist />
      <hr />
      <SongPlaylist />
    </div>
  );
};
```

# Multiple State Updates

Ideas to Reset Both Lists
1. Have the movieSlice's 'reset' function update the list of songs
- Can't be done, WON'T WORK
- Reducer functions in a slice **can't see or change state** that is being produced by another slice
2. Dispatch two separate actions
- add reset function inside songsSlice
- This would work, but it isn't the Redux way
3. Get the songsSlice to watch for the existing reset action
4. Create a new, standalone reset action and get both slices to watch for it

# Understanding Action Flow

Option 3.
- 3 and 4 are rather similar.

![How dispatch function works](https://i.imgur.com/Af2EynH.png)

When an action is dispatched, it is sent to *every* reducer in the store
- Up to this point, we haven't really cared about this, because the "combined" reducers are configured to *only care about particular action types*
- Here's our goal: convince the "Combined Songs Reducer" that it should also care about "movie/reset"

# Watching for Other Actions

extraReducers
- Whenever the slice is created by redux toolkit, this extraReducers function is going to be called automatically
- The builder object is how we tell our combined reducer to watch about some additional action types

```js
// "./store/index.js"
import { configureStore, createSlice } from "@reduxjs/toolkit";

const moviesSlice = createSlice({
  name: "movie",
  initialState: [],
  reducers: {
    addMovie(state, action) {
      state.push(action.payload);
    },
    removeMovie(state, action) {
      const idx = state.indexOf(action.payload);
      state.splice(idx, 1);
    },
    reset(state, action) {
      return [];
    }
  }
});

const songsSlice = createSlice({
  name: "song",
  initialState: [],
  reducers: {
    addSong(state, action) {
      state.push(action.payload);
    },
    removeSong(state, action) {
      const idx = state.indexOf(action.payload);
      state.splice(idx, 1);
    }
  },
  extraReducers: (builder) => {
    builder.addCase("movie/reset", (state, action) => {
      return [];
    });
  }
});

const store = configureStore({
  reducer: {
    songs: songsSlice.reducer,
    movies: moviesSlice.reducer
  }
});

export { store };
export const { addSong, removeSong } = songsSlice.actions;
export const { addMovie, removeMovie, reset } = moviesSlice.actions;
```

# Getting an Action Creator's Type

Change string action type
- `moviesSlice.actions.reset.toString()` or `moviesSlice.actions.reset.type` is a correct way
- But, `moviesSlice.actions.reset` itself does work

```js
// "./store/index.js"
//...
const songsSlice = createSlice({
  //...
	extraReducers: (builder) => {
		builder.addCase(moviesSlice.actions.reset, (state, action) => {
			console.log(moviesSlice.actions);
			return [];
		});
	}
});
//...
```

# Manual Action Creation

We now have some little bit of dependency between songs reducer and movies reducer
- If we rename "reset" in moviesSlice, then songsSlice would not work

![New Action](https://i.imgur.com/u4Pe8IQ.png)

New Goal
- Get both the Combined Songs Reducer *and* the Combined Movies Reducer to watch for an action of **"app/reset"**

```js
// "./store/index.js"
import { configureStore, createSlice, createAction } from "@reduxjs/toolkit";

export const reset = createAction("app/reset");

const moviesSlice = createSlice({
	name: "movie",
	initialState: [],
	reducers: {
		addMovie(state, action) {
			state.push(action.payload);
		},
		removeMovie(state, action) {
			const idx = state.indexOf(action.payload);
			state.splice(idx, 1);
		},
	},
	extraReducers: (builder) => {
		builder.addCase(reset, (state, action) => {
			return [];
		});
	}
});

const songsSlice = createSlice({
	name: "song",
	initialState: [],
	reducers: {
		addSong(state, action) {
			state.push(action.payload);
		},
		removeSong(state, action) {
			const idx = state.indexOf(action.payload);
			state.splice(idx, 1);
		}
	},
	extraReducers: (builder) => {
		builder.addCase(reset, (state, action) => {
			return [];
		});
	}
});

const store = configureStore({
	reducer: {
		songs: songsSlice.reducer,
		movies: moviesSlice.reducer
	}
});

export { store };
export const { addSong, removeSong } = songsSlice.actions;
export const { addMovie, removeMovie } = moviesSlice.actions;
```

# File and Folder Structure

Organize by "Function"

src/
- components : All components go in here
  - MoviesList.js
  - SongsList.js
- store/
  - actions/
    - actions.js
  - slices : All slices go in here
    - moviesSlice.js
    - songsSlice.js
  - index.js : Create and export the Redux store here
- App.js
- index.js

Organize by "Feature"

src/
- movies : *Everything* related to the **"movies"** feature goes here
  - MovieList.js
  - moviesSlice.js
- songs : *Everything* related to the **"songs"** feature goes here
  - SongsList.js
  - songsSlice.js
- store
  - actions.js
  - index.js
- App.js
- index.js

- Redux docs recommend a "feature" approach
- Redux Toolkit docs don't make a direct recommendation
- "Feature" approach frequently **does not** work well with Redux Toolkit
- **We are going to use the "Function" approach in this course**

src/
- components
  - MoviesList.js (import from moviesSlice.js)
  - SongsList.js (import from songsSlice.js)
- store/
  - slices
    - moviesSlice.js
    - songsSlice.js
  - actions.js
  - index.js
- App.js
- index.js

- Allowing components to import from slice files gets really messy
- We do not want to do this

- components
  - MoviesList.js (import from index.js)
  - SongsList.js (import from index.js)
- store/
  - slices
    - moviesSlice.js
    - songsSlice.js
  - actions.js
  - index.js
- App.js
- index.js

- store/index.js will be the central access point for *everything* related to redux
- This is going to help solve some "circular imports" issues that we will deal with later on

# Refactoring the Project Structure

```js
// "./store/index.js"
import { configureStore } from "@reduxjs/toolkit";
import { songsReducer, addSong, removeSong } from "./slices/songsSlice";
import { moviesReducer, addMovie, removeMovie } from "./slices/moviesSlice";
import { reset } from "./actions";

const store = configureStore({
	reducer: {
		songs: songsReducer,
		movies: moviesReducer
	}
});

export { store, addSong, removeSong, addMovie, removeMovie, reset };
```

```js
// "./store/actions.js"
import { createAction } from "@reduxjs/toolkit";

export const reset = createAction("app/reset");
```

```js
// "./store/slices/moviesSlice.js";
import { createSlice } from "@reduxjs/toolkit";
import { reset } from "../actions";

const moviesSlice = createSlice({
	name: "movie",
	initialState: [],
	reducers: {
		addMovie(state, action) {
			state.push(action.payload);
		},
		removeMovie(state, action) {
			const idx = state.indexOf(action.payload);
			state.splice(idx, 1);
		}
	},
	extraReducers: (builder) => {
		builder.addCase(reset, (state, action) => {
			return [];
		});
	}
});

export const { addMovie, removeMovie } = moviesSlice.actions;
export const moviesReducer = moviesSlice.reducer;
```

```js
// "./store/slices/songsSlice.js";
import { createSlice } from "@reduxjs/toolkit"
import { reset } from "../actions";

const songsSlice = createSlice({
	name: "song",
	initialState: [],
	reducers: {
		addSong(state, action) {
			state.push(action.payload);
		},
		removeSong(state, action) {
			const idx = state.indexOf(action.payload);
			state.splice(idx, 1);
		}
	},
	extraReducers: (builder) => {
		builder.addCase(reset, (state, action) => {
			return [];
		});
	}
});

export const { addSong, removeSong } = songsSlice.actions;
export const songsReducer = songsSlice.reducer;
```
