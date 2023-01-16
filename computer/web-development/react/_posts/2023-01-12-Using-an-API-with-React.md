---
title: "[React] Image Search with Unsplash API"
---

# App Overview

![Preview](https://imgur.com/keWxWCl.png)

## Features

- Handles `<input />` element.
- Uses [Unsplash.com](https://unsplash.com) an API.
  - `async`, `await` javascript keywords.
- Renders grid images.

## Installed Libraries

- `axios`

## Component Design

![Component Design](https://i.imgur.com/RNT6nlZ.png)

- `App`: Organize all components at the top.
- `SearchBar`: Capture search term from user and send the information to `App` component.
- `ImageList`: Take an array of images, map each image to `ImageShow` component.
- `ImageShow`: Responsible for showing one individual image.


# Unsplash API

![Request and Response Flow](https://i.imgur.com/ae90kWn.png)

Data fetching process
1. Our app is going to make a request over to the API.
2. The API is going to send back a response.

Notes
- React has no tools, objects, functions for making HTTP requests.
- React only cares about showing content and handling user events.
- Therefore we can write a lot of business logic and data fetching *without worrying about React.*

## Prerequisites

1. Sign up for an account.
2. Create an app to get an Access Key

## Documentation

Unsplash Developers Documentation
- [Location](https://unsplash.com/documentation#schema)
  - The API is available at `https://api.unsplash.com/`.
- [Authorization](https://unsplash.com/documentation#authorization)
  - Pass your application's access key via the HTTP Authorization header:
  - `Authorization: Client-ID YOUR_ACCESS_KEY`
- [Search photos](https://unsplash.com/documentation#search-photos)
  - Get a single page of photo results for a query.
  - `GET /search/photos?query=SEARCH_TERMS`

# Making an HTTP Request with `axios`

To make requests, we commonly use either **Axios** or **Fetch**.
- We use `axios` in this app.

```js
// axios.get format
axios.get(url, {
  headers: {
    // Headers that we want to add into the request
  },
  params: {
    // Key-value pairs that will be turned into
    // a query string and added to the URL
  },
});
```

## About `async` and `await`

Javascript does not automatically pause when you make a request.

```js
const fetchData = () => {
  const response = makeRequest(); // JS starts the request...
  console.log(response); // ...and then instantly runs this line.
  // Response has not been received.
}
```

Javascript should not try to work with the response until it has actually been received.
- Use async/await to wait for the request to finish before moving on.

```js
const fetchData = async () => { // 'async' is a requirement when you use 'await'.
  const response = await axios.get(...); // 'await' keyword tells JS to wait for a response.
  console.log(response); // Now the response is ready.
}
```

## Unsplash API Image Search Request

- We create a new `api.js` file.
- It contains all the code related to contacting the Unsplash API.
- Instead of `response`, return an array of images(`response.data.results`).

```js
// "./api.js"
import axios from "axios";

const AccessKey = "..."; // YOUR_ACCESS_KEY

const searchImages = async (term) => {
  const response = await axios.get("https://api.unsplash.com/search/photos", {
    headers: {
      Authorization: `Client-ID ${AccessKey}`
    },
    params: {
      query: term
    }
  });

  // return response; // It should work. Check it with console.log(response)
  return response.data.results;
};

export default searchImages;
```

# Thinking About Data Flow

- Where do we do data fetching?
- Where do we define state?
- How do we share information between components?

Fact of this app
- The `SearchBar` component contains the text input a user will type into.
- The user pressing 'enter' key in the text input means we need to do a search.
- We have a function that will turn a search term into an array of image objects.
- The array of image objects need to get into the `ImageList` component.

Terms
- Search term belongs to `SearchBar` component.
- Images belong to `ImageList` component.
- The problem is `searchImages()` function.

## Component Design

![Component Design](https://i.imgur.com/diMJQ2T.png)

- Sibling components cannot directly communicate.
- Sibling components can share information by involving the parent component.
- `App` component can send the list of images down to `ImageList` component by using props.
- Challenging: When the user presses the 'enter' key, we need to get the search term up to the `App`.
  - Child to parent communication.

# Child to Parent Communcation

# Implementing Child to Parent Communication

![Information Flow](https://i.imgur.com/0iYZtFG.png)

- Pass an event handler down.
- Call the event handler when something happens.

```jsx
import SearchBar from "./components/SearchBar";
import searchImages from "./api";

const App = () => {
  const handleSubmit = (term) => {
    searchImages(term);
  }

  return (
    <div>
      <SearchBar onSubmit={handleSubmit} />
    </div>
  );
};

export default App;
```

# Handling Form Submission

Press me "Enter" Key technique.
- put `<input />` inside `<form>`.
- HTML technique (Nothing to do with React, JS)
- When pressed "Enter", form will trigger `onSubmit` event.

But... `<form>` tag does something weird when press "Enter".
- Trigger browser event.
- Default behavior around forms.
- Browser gather information in the form and then make network request
  - `<form><input name="somename" value="someinput" /></form>`
  - When entered, request "localhost:3000/somename=someinput"

How to prevent default behavior of an event?
- receive `event` argument in the event handler.
- `event.preventDefault();`

```jsx
import React from "react";

const SearchBar = ({ onSubmit }) => {
  const handleFormSubmit = (event) => {
    event.preventDefault();
    // onSubmit(term);
  }

  return (
    <div>
      <form onSubmit={handleFormSubmit} >
        <input />
      </form>
    </div>
  );
};

export default SearchBar;
```

Is it okay to use this technique or is it too overboard?
- Totally okay to use this.

# Handling Input Elements

How to extract string in the input element
- `document.querySelector("input").value` &larr; NEVER DO THIS

## Handling Text Inputs
1. Create a new piece of state.
2. Create an event handler to watch for the `onChange` event.
3. When the `onChagne` event fires, get the value from the input.
4. Take that value from the input and use it to update your state.
5. Pass your state to input as the value prop.

```jsx
import { useState } from "react";

const SearchBar = ({ onSubmit }) => {
  const [term, setTerm] = useState("");

  const handleChange = (event) => {
    setTerm(event.target.value);
  }

  const handleFormSubmit = (event) => {
    event.preventDefault();
    onSubmit(term);
    // onSubmit(document.querySelector("input").value); // NEVER DO THIS
  }

  return (
    <div>
      <form onSubmit={handleFormSubmit} >
        <input value={term} onChange={handleChange} />
      </form>
    </div>
  );
};

export default SearchBar;
```

1. User types in `input`.
2. Browser updates the text in the `input`.
3. Browser triggers an event to say the `input` was updated.
- 2, 3 are normal browser behaviors.
4. We read the value from the input, update state.
5. State updated, and component rerenders.
6. We provide "value" prop to `input` - React Changes the `input`'s value.

WHY??
- Stealing control of the value of the `input` element from the browser.
- put the value of the `input` under the control of a state system.
- Need to read the value of the `input`?
  - Look at `term` state
  - NO  `document.querySelector("input").value`
- Update the value of the `input`?
  - Call `setTerm`
  - NO  `document.querySelector("input").value = ...`
- component re-renders with every keypress 
  - Very easy to add in more advanced features.

# Reminder on `async` and `await`

- `searchImages(term)` returns `Promise`.
  - The request is not yet completed.
- We need to tell JS that when calling `searchImages`, this is an asynchronous function.
  - We do not want to continue execution until `searchImages` finished running.
  - `await` again.
- `handleSubmit` becomes also an asynchronous function as well.

```jsx
import SearchBar from "./components/SearchBar";
import searchImages from "./api";

const App = () => {
  /*
  const handleSubmit = (term) => {
    const results = searchImages(term); // returns Promise
  }
  */
  const handleSubmit = async (term) => {
    const results = await searchImages(term);
  }

  return (
    <div>
      <SearchBar onSubmit={handleSubmit} />
    </div>
  );
};

export default App;
```

# Communicating the List of Images Down

- Use `props` to communicate from parent to child.
- App can send the list of images down to `ImageList` using props!

![images to ImageList](https://i.imgur.com/YJgRil6.png)

1. We need to communicate the array of images down to ImageList.
2. This might look confusing because we are going to combine the state system + the props system.
3. Let me give you two reminders about the state system.

- After doing a search, we want to ***update content on the screen*** with new list of images.
  - STATE!!!

```jsx
import { useState } from "react";
import SearchBar from "./components/SearchBar";
import ImageList from "./components/ImageList";
import searchImages from "./api";

const App = () => {
  const [images, setImages] = useState([]);

  const handleSubmit = async (term) => {
    const results = await searchImages(term);
    setImages(results);
  }

  return (
    <div>
      <SearchBar onSubmit={handleSubmit} />
      <ImageList images={images} />
    </div>
  );
};

export default App;
```

- When you update state, the component and **all of its children** are rerendered.
- From the perspective of `App`, images is `state`.
- From the perspective of `ImageList`, images is `props`.

# Building a List of Images

![List of images](https://i.imgur.com/GUpeuo7.png)

1. Apply a `Key` to each element during the mapping step.
2. After re-rendering, compare the keys on each `ImageShow` to the *keys from the previous render*.
3. Apply minimal set of changes to existing DOM elements.
- React does 2 and 3.

# Notes on Keys

Requirements for `Keys`
- Use whenever we have a list of elements.(so every time we do a `map`)
- Add the key to the top-most JSX element in the list.
- Must be a string or number.
- Should be unique *for this list*.
- Should be consistent across rerenders.

Which keys should I use?
- Almost all lists we ever create will be built by mapping over an array of objects fetched from a database.
- These objects will have a unique "id". This is the perfect key to use.

# Displaying Images

```jsx
const ImageShow = ({ image }) => {
  return (
    <div>
      <img alt={image.alt_description} src={image.urls.small} />
    </div>
  )
};

export default ImageShow;
```

# A Touch of Styling

```css
/* "./components/SearchBar.css" */
.search-bar {
  border: 1px solid lightgray;
  border-radius: 5px;
  padding: 10px;
}

.search-bar form {
  display: flex;
  flex-direction: column;
}
```

```css
/* "./components/ImageList.css" */
.image-list {
  columns: 6 200px;
  column-gap: 10px;
}

img {
  width: 100%;
  margin-bottom: 10px;
}
```
