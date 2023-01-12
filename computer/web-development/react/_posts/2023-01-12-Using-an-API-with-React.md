---
title: "[React] Using an API with React"
---

# App Overview

![Preview]()
Installed Libraries: Axios

# Component Design

![Component Design](https://i.imgur.com/RNT6nlZ.png)

- `App`: Organize all components at the top.
- `SearchBar`
- `ImageList`: Take an array of images, map each image to `ImageShow` component.
- `ImageShow`: Responsible for showing one individual image.

## Codes

### `index.js`

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

### `App.js`

```jsx
function App() {
  return (
    <div>App</div>
  )
}

export default App;
```

# The Path Forward

![Request and Response Flow](https://i.imgur.com/ae90kWn.png)

Think of the data fetching process first.

1. Our app is going to make a request over to the API.
2. The API is going to send back a response.

Notice.
- React has no tools, objects, functions for making HTTP requests.
- React only cares about showing content and handling user events.
- We can write a lot of business logic + data fetching *without worrying about React.*

# Understanding the Unsplash API

1. Sign up for an account.
2. Create an 'app' to get an Access Key

## Documentation

Schema -> Location\\
Authorization -> Public Authentication\\
Search -> Search photos\\

GET https://api.unsplash.com**/search/photos?query=cars**\\
Authorization: Client-ID abc123

# Making an HTTP Request

- React itself has no functions/tools for making HTTP requests.
- To make requests, we commonly use either **Axios** or **Fetch**.

```js
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
<br/>

Create a new `api.js` file.
- Contains all the code related to contacting the Unsplash API.

```js
import axios from "axios";

const AccessKey = "..."; // your AccessKey

const searchImages = async () => {
  const response = await axios.get("https://api.unsplash.com/search/photos", {
    headers: {
      Authorization: `Client-ID ${AccessKey}`
    },
    params: {
      query: "cars"
    }
  });

  return response;
};

export default searchImages;
```

The code should work. `console.log(response)` to check the code.

# Using Async:Await

Javascript does not automatically pause when you make a request.
Response has not been received yet.

```js
const fetchData = () => {
  const response = makeRequest(); // JS starts the request...
  console.log(response); // ...and then instantly runs this line.
}
```

We have to tell Javascript not to try to work with the response until it has actually been received.
```js
const fetchData = () => {
  const response = makeRequest(); // JS starts the request.

  /*
    Dear JS, please wait until we get a response
    before continuing on to the next line of code
  */

  console.log(response); // Now the response is ready.
}
```

Use async/await to tell JS to wait for the request to finish before moving on.

```js
const fetchData = async () => { // 'async' is a requirement when you use 'await'.
  const response = await axios.get(...); // 'await' keyword tells JS to wait for a response.
  console.log(response); // Now the response is ready.
}
```

# Data Fetching Cleanup

Update on `api.js`
- edit search term
- instead of `response`, return an array of images(`response.data.results`).

```js
import axios from "axios";

const AccessKey = "..."; // your AccessKey

const searchImages = async (term) => {
  const response = await axios.get("https://api.unsplash.com/search/photos", {
    headers: {
      Authorization: `Client-ID ${AccessKey}`
    },
    params: {
      query: term,
    }
  });

  return response.data.results;
};

export default searchImages;
```

# Thinking About Data Flow
