---
title: "[React] How to Handle Forms"
---

# App Overview

Store book
- store fetch update delete data

![App design](https://i.imgur.com/MyBt1Vc.png)

![Components Hierarchy](https://i.imgur.com/sEzPcQO.png)

# State Location

State updated?
- Rerender the component it is defined in + all that components children

Find all the components that need to use this state.

Define the state in the lowest common parent.

```jsx
// "./App.js"
import { useState } from "react";

const App = () => {
  const [books, setBooks] = useState([]);

  return (
    <div>Hello</div>
  );
};

export default App;
```

# Reminder on Event Handlers

- createBook
- editBook
- deleteBook

# Receiving New Titles
```jsx
// "./components/BookCreate.js"
import { useState } from "react";

const BookCreate = ({ onCreate }) => {
  const [title, setTitle] = useState("");

  const handleChange = (e) => {
    setTitle(e.target.value);
  }

  const handleSubmit = (e) => {
    e.preventDefault();
    onCreate(title);
    setTitle("");
  }

  return (
    <div>
      <form onSubmit={handleSubmit} >
        <label>Title</label>
        <input value={title} onChange={handleChange} />
        <button>Create!</button>
      </form>
    </div>
  );
};

export default BookCreate;
```

# Adding Styling

![Form style](https://imgur.com/fgiei4z.png)

# Updating State

`setBooks(Books.push(...))` is not going to work...

# Don't Mutate That State!

# Adding a Book, For Real!

```jsx
// "./App.js"
import { useState } from "react";
import BookCreate from "./components/BookCreate";

const App = () => {
  const [books, setBooks] = useState([]);

  const createBook = (title) => {
    setBooks([
      ...books,
      {
        id: 123,
        title: title
      }
    ]);
  };

  return (
    <div>
      <BookCreate onCreate={createBook}/>
    </div>
  );
};

export default App;
```

# Generating Random ID's

`let bookId = 1` 이건 왜 안됨???

```jsx
// "./App.js"
import { useState } from "react";
import BookCreate from "./components/BookCreate";

const App = () => {
  const [bookId, setBookId] = useState(1);
  const [books, setBooks] = useState([]);

  const createBook = (title) => {
    setBooks([
      ...books,
      {
        id: bookId,
        title: title
      }
    ]);
    setBookId(bookId + 1);
  };

  return (
    <div>
      <BookCreate onCreate={createBook}/>
    </div>
  );
};

export default App;
```

# Displaying the List

```jsx
// "./components/BookList.js"
import BookShow from "./BookShow";

const BookList = ({ books }) => {
  const renderedBooks = books.map((book) => {
    return <BookShow key={book.id} book={book} />;
  });

  return (
    <div className="book-list" >{renderedBooks}</div>
  );
};

export default BookList;
```

```jsx
// "./components/BookShow.js"
const BookShow = ({ book }) => {
  return (
    <div className="book-show" >{book.title}</div>
  );
};

export default BookShow;
```

# Deleting Records

```jsx
// "./App.js"
import { useState } from "react";
import BookCreate from "./components/BookCreate";
import BookList from "./components/BookList";

const App = () => {
  const [bookId, setBookId] = useState(1);
  const [books, setBooks] = useState([]);

  const deleteBookById = (id) => {
    const updatedBooks = books.filter((book) => {
      return book.id !== id;
    });

    setBooks(updatedBooks);
  };

  const createBook = (title) => {
    setBooks([
      ...books,
      {
        id: bookId,
        title: title
      }
    ]);
    setBookId(bookId + 1);
  };

  return (
    <div className="app" >
      <BookList books={books} onDelete={deleteBookById} />
      <BookCreate onCreate={createBook} />
    </div>
  );
};

export default App;
```

```jsx
// "./components/BookList.js"
import BookShow from "./BookShow";

const BookList = ({ books, onDelete }) => {
  const renderedBooks = books.map((book) => {
    return <BookShow key={book.id} book={book} onDelete={onDelete} />;
  });

  return (
    <div className="book-list" >{renderedBooks}</div>
  );
};

export default BookList;
```

```jsx
// "./components/BookShow.js"
const BookShow = ({ book, onDelete }) => {
  const handleClick = () => {
    onDelete(book.id);
  };

  return (
    <div className="book-show" >
      {book.title}
      <div className="actions">
        <button className="delete" onClick={handleClick} >
          Delete
        </button>
      </div>
    </div>
  );
};

export default BookShow;
```

# Toggling Form Display
