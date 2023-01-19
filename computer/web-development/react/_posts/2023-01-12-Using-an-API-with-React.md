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

![Component Design](https://i.imgur.com/D5sv4ZR.png)

- Sibling components cannot directly communicate.
- Sibling components can share information by involving the parent component.
- `App` component can send the list of images down to `ImageList` component by using props.
- Challenging: When the user presses the 'enter' key, we need to get the search term up to the `App`.
  - Child to parent communication.

## Child to Parent Communcation

![Information Flow](https://i.imgur.com/OT3jhQl.png)

- Parent component pass an event handler down to child component.
- Child component calls the event handler when something happens.
- ```jsx
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

# Handling SearchBar Component

Now we want to make a `<SearchBar />` component.
- `<SearchBar />` component should know what the input value is.
- When press `Enter` key, `<SearchBar />` component will gather information and make a network request.

## Handling Form Submission

Default behavior of `<form>`.
- When press `Enter`, browser gathers information in the `<form>` tag and triggers a network event.
  - ```html
    <form>
      <input name="somename" value="somevalue" />
    </form>
    ```
  - In this case, the browser will request `https://localhost:3000/?somename=somevalue`.
- In Javascript, `event.preventDefault()` will prevent this event from being triggered.
  - ```jsx
    const SearchBar = () => {
      const handleFormSubmit = (event) => {
        event.preventDefault();
      }

      return (
        <div>
          <form onSubmit={handleFormSubmit} />
        </div>
      );
    };
    ```

## Handling Input Elements

We want to handle string in the input element.
- Controlled component
1. Create a new piece of state.
2. Create an event handler to watch for the `onChange` event.
3. When the `onChagne` event fires, get the value from the input.
4. Take that value from the input and use it to update your state.
5. Pass your state to input as the value prop.

- In this manner, we can put control of the value of the `input` element under the state system.
- `document.querySelector("input").value` &larr; **NEVER DO THIS**

```jsx
import { useState } from "react";

const SearchBar = ({ onSubmit }) => {
  const [term, setTerm] = useState("");

  const handleChange = (event) => {
    setTerm(event.target.value);
  }

  return (
    <div>
      <input value={term} onChange={handleChange} />
    </div>
  );
};
```
- Need to read the value of the `input`?  &rarr; Look at `term` state.
- Update the value of the `input`?  &rarr; Call `setTerm(...)`.
- Component rerenders with every keypress.  &rarr; Easy to add in more advanced features.

# Reminder on `async` and `await`

We need to tell JS that `searchImages` is an asynchronous function.
- `searchImages(term)` is an asynchronous function which returns `promise`.
- So we have to use `await` keyword again.
- Consequently, `handleSubmit` becomes an asynchronous function as well.

```jsx
import SearchBar from "./components/SearchBar";
import searchImages from "./api";

const App = () => {
  const handleSubmit = async (term) => {
    const results = await searchImages(term);
    // ...
  }

  return (
    <div>
      <SearchBar onSubmit={handleSubmit} />
    </div>
  );
};
```

# Rendering the List of Images

Finally, `<App />` component will pass list of images down to `<ImageList />` component.

## Passing the List of Images Down

`<App />` can send the list of images down to `<ImageList />` by using props.

- ![images to ImageList](https://i.imgur.com/N5SzJL4.png)
- After doing a search, we want to ***update content on the screen*** with new list of images.
  - We need to use *state* system.
- When a component updates state, the component and **all of its children** are rerendered.

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

## Building a List of Images

![List of images](https://i.imgur.com/tLGiPaL.png)

Process of rendering list of components.
1. Apply a `key` to each element during the mapping step.
2. After rerendering, compare the keys on each `<ImageShow />` to the *keys from the previous render*.
3. Apply minimal set of changes to existing DOM elements.
- React does 2 and 3.

```jsx
// "./ImageList.js"
import ImageShow from "./ImageShow";

const ImageList = ({ images }) => {
  const renderedImages = images.map(image => {
    return <ImageShow key={image.id} image={image} />
  });

  return (
    <div className="image-list">
      {renderedImages}
    </div>
  )
}

export default ImageList;
```

## Notes on Keys

Requirements for `keys`.
- Use whenever we have a list of elements.
- Add the key to the top-most JSX element in the list.
- Must be a string or number.
- Should be unique *for this list*.
- Should be consistent across rerenders.

Which keys should I use?
- Almost all lists of objects are fetched from a database.
- These objects will have a unique `id`.

## Displaying Images

```jsx
// "./components/ImageShow.js"
const ImageShow = ({ image }) => {
  return (
    <div>
      <img alt={image.alt_description} src={image.urls.small} />
    </div>
  )
};

export default ImageShow;
```

## Minor CSS Styling

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
