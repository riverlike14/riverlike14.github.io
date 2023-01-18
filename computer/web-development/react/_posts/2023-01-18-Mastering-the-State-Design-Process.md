---
title: "[React] Mastering the State Design Process"
---

# App Overview

## Goal

- How to organize project files.
- Create an accordion component.
- Learn state design process.
- Handles event handler in `map` function.

## Library

- `react-icons`

# Project Organization

How to organize different files.

![Page and components](https://i.imgur.com/Zv5WkPS.png)

- **Component**
  - Reusable React component that shows a handful elements.
  - Button, ItemShow, ProductList, Dropdown, SearchBar, Checkbox, ...
- **Page**
  - Still a React component. But not intended to be reused.
  - CheckoutPage, ProductPage, LoginPage, LandingPage, RentalPage, CartPage, ...
- There are no technical distinctions between the two.

## Grouping Designs

- Grouping by feature
  - ![Possible downside of grouping by features](https://i.imgur.com/kYdwSn0.png)
  <!-- ![Grouping by feature](https://i.imgur.com/7v07ngB.png)-->
  - Single component might be needed in many locations.
- Grouping by type
  - ![Grouping by type](https://i.imgur.com/ZEKACYh.png)
  - Each folder can grow really quickly.
- Grouping by both type and feature
  - ![Grouping by type and feature](https://i.imgur.com/2yn0uLX.png)
  - Add more organization with subfolders.

## Tips about File/Folder Organization

- Do not worry about this too much.
- 99% of professional prjects - a senior engineer is going to decide for you.
- On any large project, you'll end up using search to find files, rather than manual navigation.
- Having a bunch of files in a folder is not inherently bad.


# Component Overview

![Accordion preview](https://imgur.com/GBxsGZ5.png)

- Accordion.
- When a header is clicked, it expands its contents and collapses all the others'.

## Component Structure

![Components of an accordion](https://i.imgur.com/bmqcewW.png)

- Accordion consists of labels and contents.
- Accordion gets data from the `App` component.
  - ![Accordion component](https://i.imgur.com/4iUseYP.png)
  - Each object would be look like `{ id:, label: , content:  }`

## Component Setup

Simple boilerplates of `App.js` and `Accordion.js`

- ```jsx
  // "./App.js"
  import Accordion from "./components/Accordion";

  const App = () => {
    const items = [
      {
        id: 0, 
        label: "Can I use React on a project?",
        content: "You can use React on any project you want.
          You can use React on any project you want.",
      },
      {
        id: 1, 
        label: "Can I use CSS on a project?",
        content: "You can use CSS on any project you want.
          You can use CSS on any project you want.",
      },
      {
        id: 2, 
        label: "Can I use Javascript on a project?",
        content: "You can use Javascript on any project you want.
          You can use Javascript on any project you want.",
      },
    ]

    return (
      <Accordion items={items} />
    )
  };

  export default App;
  ```

- ```jsx
  // "./components/Accordion.js"

  const Accordion = ({ items }) => {
    const renderedItems = items.map((item) => {
      return (
        <div key={item.id}>
          <div>{item.label}</div>
          <div>{item.content}</div>
        </div>
      )
    });

    return (
      <div>{renderedItems}</div>
    );
  };

  export default Accordion;
  ```

# State Design Process Overview

We want to expand some section and hide another whenever user clicks one of the items.
- In other words, we want to change some content on the screen whenever user does something inside of our app.
- This means that we are going to use *state system*.

Main questions when dealing with *state system*:
- When should I use state?
  - When do we need to change content on the screen?
- What should it be called?
- What type of data will my state be?
- Which component should it be defined in?

## Event + State Design Process

1. List out what a user will do and *changes* they will see while using your app.
2. Categorize each step as state or event handler.
3. Group common steps. Remove duplicates. Rewrite descriptions.
- 1~3: **What states and event handlers are there?**
4. Look at mockup. Remove or simplify parts that aren't changing.
5. Replace remaining elements with text descriptions
6. Repeat 4 and 5 with a different variation.
7. Imagine you have to write a function that returns the text of steps 5 and 6. In addition to your component props, *what other arguments would you need?*
- 4~7: **What should be the name and the type of states and event handlers?**
8. Decide where each event handler + state will be defined.
- 8: **Where should it be defined?**

## State Design Process Of Accordion

We are going to apply teh state design process on our accordion component.

### Step 1

List out what a user will do and *changes* they will see while using your app.
- Clicked on third section.
- First section collapsed.
- Third section expanded.
- Clicked on second section.
- Third section collapsed.
- Second section expands.

We can observe the followings:
- User sees something on the screen changes.
  - We probably need **state** to implement this.
- User committed some action.
  - We probably need an **event handler** to implement this.

### Step 2

Categorize each step as state or event handler.
- Clicked on third section. &rarr; **event handler**
- First section collapsed. &rarr; **state**
- Third section expanded. &rarr; **state**
- Clicked on second section. &rarr; **event handler**
- Third section collapsed. &rarr; **state**
- Second section expanded. &rarr; **state**

### Step 3

Group common steps. Remove duplicates. Rewrite descriptions.
- Clicked on a section header. &rarr; **event handler**
- One section is expanded, all others are collapsed. &rarr; **state**

### Step 4
Look at mockup. Remove or simplify parts that aren't changing.

- Some parts are not changing when user clicks
  - Section title never change.
  - Text in content area is not changing.

### Step 5

Replace remaining elements with text descriptions

- ![Step 5](https://i.imgur.com/5p235Zs.png)
- Imagine that you tell to some other engineer who cannot see your screen, what the UI currently looks like.

### Step 6

Repeat step 4 and step 5 with a different variation.

### Step 7

Imagine you have to write a function that returns the text of steps 5 and 6. In addition to your component props, *what other arguments would you need?*

- Notes on types of state
  - State can be a number, boolean, string, array or object.
  - ***Ideally, avoid arrays/objects***
    - Handling arrays and objects are challenging.
  - One text section is visible &rarr; state &rarr; number? boolean? string?

Imagine that we have to define a function that returns "expanded" or "collapsed".
- What type would this argument have to be...
  ```js
  const result = myFunction(items, /* ??? */);
  console.log(result); // ["expanded", "collapsed", "collapsed"]
  ```
  ...to give us these outputs?
  ```js
  // Possible solution
  const result = myFunction(items, expandedIndex) {
    return items.map((item, idx) => {
      if (idx === expandedIndex)
        return "expanded";
      else
        return "collapsed";
    })
  }
  ```
- Preliminary Design
  - Clicking on a section header &rarr; Event Handler.
  - One section expanded, all others collapsed. &rarr; State.
  - `expandedIndex` &rarr; a number type.

### Step 8

Decide where each event handler + state will be defined.

- Case 1
  - `expandedIndex` is in App and passed down to Accordion.
- Case 2
  - `expandedIndex` is in Accordion.
- Question: Should this state be defined in the parent or the child?
  - Does any component besides Accordion *reasonably* need to know which item is expanded?
  - Yes &rarr; Define in App.
    - Because React is not good at *directly* communicating data between siblings.
  - No &rarr; Define in Accordion.
- Event handler should be usually **defined** in same component as state it modifies.
  - It can be **used** in a different component.

### Design Result

- ![Final Design](https://i.imgur.com/kVfgB1l.png)
- ```jsx
  // "./components/Accordion.js"
  import { useState } from "react";

  const Accordion = ({ items }) => {
    const [expandedIndex, setExpandedIndex] = useState(0);

    const renderedItems = items.map((item, index) => {
      const isExpanded = index === expandedIndex;

      return (
        <div key={item.id}>
          <div>{item.label}</div>
          <div>{item.content}</div>
        </div>
      )
    });

    return (
      <div>{renderedItems}</div>
    );
  };

  export default Accordion;
  ```

# Conditional Rendering

We want to conditionally render `<div>{item.content}</div>`.
- We don't want to render it, not hiding it with CSS.

React does not print `booleans`, `null` and `undefined`.
- `||` gives back the first value that is truthy.
  - Useful when setting default value.
  - `{ message || defaultMessage }` would print some message.
- `&&` gives back the first falsey value or the last truthy value.
  - Useful when conditional rendering.
- `{isExpanded && <div>{item.content}</div>}` does the work.


# Event Handlers in `map` Function

## Inline Event Handlers

Longhand version
- ```jsx
  function ProductShow() {
    const handleClick = () => {
      console.log("Clicked!");
    }

    return <div onClick={handleClick}></div>
  }
  ```
- Use when handler has more than one lines of code.
- Slightly harder to use with lists.

Shorthand version
- ```jsx
  function ProductShow() {
    return <div onClick={() => console.log("Clicked!")}></div>
  }
  ```
- Use when handler has 1 line of code.
- Can make JSX harder to read.

We can mix both versions
- ```jsx
  // "./components/Accordion.js"
    // ...
    const renderedItems = items.map((item, index) => {
      const isExpanded = index === expandedIndex;

      return (
        <div key={item.id}>
          <div onClick={() => setExpandedIndex(index)}>{item.label}</div>
          {isExpanded && <div>{item.content}</div>}
        </div>
      )
    });
    // ...
  ```
- It is still hard to read.
- Note that each function is totally different:
  - `() => setExpandedIndex(0)`
  - `() => setExpandedIndex(1)`
  - `() => setExpandedIndex(2)`

## Variation on Event Handlers

```jsx
// "./components/Accordion.js"
import { useState } from "react";

const Accordion = ({ items }) => {
  const [expandedIndex, setExpandedIndex] = useState(0);

  const renderedItems = items.map((item, index) => {
    const isExpanded = index === expandedIndex;

    const handleClick = () => {
      setExpandedIndex(index);
    }

    return (
      <div key={item.id}>
        <div onClick={handleClick}>{item.label}</div> 
        {isExpanded && <div>{item.content}</div>}
      </div>
    )
  });

  return (
    <div>{renderedItems}</div>
  );
};

export default Accordion;
```

- Working fine.
- When adding logics inside the mapping function, mapping function becomes that much harder to read and understand.
  - If `handleClick` has many lines, or there are many event handlers inside the mapping function, it is really hard to understand

```jsx
// "./components/Accordion.js"
import { useState } from "react";

const Accordion = ({ items }) => {
  const [expandedIndex, setExpandedIndex] = useState(0);

  const handleClick = (nextIndex) => {
    setExpandedIndex(nextIndex);
  }

  const renderedItems = items.map((item, index) => {
    const isExpanded = index === expandedIndex;

    return (
      <div key={item.id}>
        <div onClick={() => handleClick(index)}>{item.label}</div> 
        {isExpanded && <div>{item.content}</div>}
      </div>
    )
  });

  return (
    <div>{renderedItems}</div>
  );
};

export default Accordion;
```
- We can define event handler outside the mapping function, easy to read
- When we using mapping function and want to define event handler outside the mapping function
- Usually event handlers receive `event` object for the first argument
  - `<div onClick={handleClick} />` runs `handleClick(event)` when clicked.

# Conditional Icon Rendering

```jsx
// "./components/Accordion.js"
import { useState } from "react";

const Accordion = ({ items }) => {
  const [expandedIndex, setExpandedIndex] = useState(0);

  const handleClick = (nextIndex) => {
    setExpandedIndex(nextIndex);
  }

  const renderedItems = items.map((item, index) => {
    const isExpanded = index === expandedIndex;

    const icon = <span>{isExpanded ? "DOWN" : "LEFT"}</span>;

    return (
      <div key={item.id}>
        <div onClick={() => handleClick(index)}>
          {icon}
          {item.label}
        </div> 
        {isExpanded && <div>{item.content}</div>}
      </div>
    )
  });

  return (
    <div>{renderedItems}</div>
  );
};

export default Accordion;
```

# Displaying Icons

```jsx
// "./components/Accordion.js"
import { useState } from "react";
import { RxDoubleArrowLeft, RxDoubleArrowDown } from "react-icons/rx";

const Accordion = ({ items }) => {
  const [expandedIndex, setExpandedIndex] = useState(0);

  const handleClick = (nextIndex) => {
    setExpandedIndex(nextIndex);
  }

  const renderedItems = items.map((item, index) => {
    const isExpanded = index === expandedIndex;

    const icon = <span>
      {isExpanded ? <RxDoubleArrowDown /> : <RxDoubleArrowLeft />}
    </span>;

    return (
      <div key={item.id}>
        <div onClick={() => handleClick(index)}>
          {icon}
          {item.label}
        </div> 
        {isExpanded && <div>{item.content}</div>}
      </div>
    )
  });

  return (
    <div>{renderedItems}</div>
  );
};

export default Accordion;
```

# Adding Styling

```jsx
// "./components/Accordion.js"
import { useState } from "react";
import { RxDoubleArrowLeft, RxDoubleArrowDown } from "react-icons/rx";

const Accordion = ({ items }) => {
  const [expandedIndex, setExpandedIndex] = useState(0);

  const handleClick = (nextIndex) => {
    setExpandedIndex(nextIndex);
  }

  const renderedItems = items.map((item, index) => {
    const isExpanded = index === expandedIndex;

    const icon = (
      <span className="text-xl">
        {isExpanded ? <RxDoubleArrowDown /> : <RxDoubleArrowLeft />}
      </span>
    );

    return (
      <div key={item.id}>
        <div
          className="flex justify-between p-3 bg-gray-50 border-b items-center cursor-pointer"
          onClick={() => handleClick(index)}
        >
          {item.label}
          {icon}
        </div> 
        {isExpanded && <div className="border-b p-5">{item.content}</div>}
      </div>
    )
  });

  return (
    <div className="border-x border-t rounded">{renderedItems}</div>
  );
};

export default Accordion;
```

# Toggling Panel Collapse

Change default index -1

```jsx
// "./components/Accordion.js"
import { useState } from "react";
import { RxDoubleArrowLeft, RxDoubleArrowDown } from "react-icons/rx";

const Accordion = ({ items }) => {
  const [expandedIndex, setExpandedIndex] = useState(-1);

  const handleClick = (nextIndex) => {
    if (expandedIndex === nextIndex) {
      setExpandedIndex(-1);
    } else {
      setExpandedIndex(nextIndex);
    }
  }

  const renderedItems = items.map((item, index) => {
    const isExpanded = index === expandedIndex;

    const icon = (
      <span className="text-xl">
        {isExpanded ? <RxDoubleArrowDown /> : <RxDoubleArrowLeft />}
      </span>
    );

    return (
      <div key={item.id}>
        <div
          className="flex justify-between p-3 bg-gray-50 border-b items-center cursor-pointer"
          onClick={() => handleClick(index)}
        >
          {item.label}
          {icon}
        </div> 
        {isExpanded && <div className="border-b p-5">{item.content}</div>}
      </div>
    )
  });

  return (
    <div className="border-x border-t rounded">{renderedItems}</div>
  );
};

export default Accordion;
```

# Delayed State Updates

Optional

![Two clicks not working](https://imgur.com/iEeSGhK.png)

- `$0` refers to the first div.
- when `$0.click();`, the first content hides.
- However, two times of `$0.click()` is not opening the content again.

What we want(which is not happening now)
- expandedIndex === 0
- User clicks first header(index 0)
- Event handlers executed
- Because expandedIndex === index, we call `setExpandedIndex(-1)`
- Component rerenders, first section is collapsed, expandedIndex === -1
- User clicks first header again(index 0)
- Event handler executed

What is actually happening(bad)
- expandedIndex === 0
- User clicks first header(index 0)
- Event handlers executed
- Because expandedIndex === index, we call `setExpandedIndex(-1)`
- React: Oh, you want to update state... yeah, I'll get around to it in the future...
- User clicks first header again(index 0)
- Event handler executed
- expandedIndex hasn't been updated yet!
- Because expandedIndex === index, we call `setExpandedIndex(-1)`
- React: Oh, you want to update state... yeah, I'll get around to it in the future...
  - Try to `console.log(expandedIndex)` in `handleClick` if you want to check
- Time passes...
- React: Guess I'll finally do that state update
- expandedIndex === -1
- First panel is collapsed

# Functional State Updates

- Option 1.
  - Get React to process state updates instantly
  - React is assuming that there might be some other piece of states to update more or less at the same time
  - Little delay is refer to fetching. React allows us to several updates. React then process this updates.
  - Possible solution, but our app is going to be a little bit slower.
- Option 2.
  - Get access to the most up to date value of `expandedIndex` in `handleClick`
  - At `setExpandedIndex(-1)`, somewhere, react queued up this instruction and is going to process this instruction at some point of time.

Option 2.

```jsx
const handleClick = () => {
  setCounter(10);
}
```
- Simple version
- Use if new value doe not depend on old

```jsx
const [counter, setCounter] = useState(0);

const handleClick = () => {
  setCounter(currentCounter => { // currentCounter is guaranteed to be the most
                                 // up to date version of counter
    if (currentCounter > 10) {
      return 20;
    } else {
      return currentCounter + 1; // counter will be updated to whatever
                                 // value we return from this function
    }
  });
};
```
- Functional version
- Use if new value depends on old
- Technically only an issue if state updates occur *really quickly...* you can get pretty far only  using the simple version.

```jsx
// "./components/Accordion.js"
import { useState } from "react";
import { RxDoubleArrowLeft, RxDoubleArrowDown } from "react-icons/rx";

const Accordion = ({ items }) => {
  const [expandedIndex, setExpandedIndex] = useState(-1);

  const handleClick = (nextIndex) => {
    console.log("STALE version of expandedIndex:", expandedIndex);
    setExpandedIndex(currentExpandedIndex => {
      console.log("UP TO DATE version of expandedIndex:", currentExpandedIndex);
      if (currentExpandedIndex === nextIndex) {
        return -1;
      } else {
        return nextIndex;
      }
    })
  }
  /*
  const handleClick = (nextIndex) => {
    if (expandedIndex === nextIndex) {
      setExpandedIndex(-1);
    } else {
      setExpandedIndex(nextIndex);
    }
  }
  */

  const renderedItems = items.map((item, index) => {
    const isExpanded = index === expandedIndex;

    const icon = (
      <span className="text-xl">
        {isExpanded ? <RxDoubleArrowDown /> : <RxDoubleArrowLeft />}
      </span>
    );

    return (
      <div key={item.id}>
        <div
          className="flex justify-between p-3 bg-gray-50 border-b items-center cursor-pointer"
          onClick={() => handleClick(index)}
        >
          {item.label}
          {icon}
        </div> 
        {isExpanded && <div className="border-b p-5">{item.content}</div>}
      </div>
    )
  });

  return (
    <div className="border-x border-t rounded">{renderedItems}</div>
  );
};

export default Accordion;
```

![Result](https://imgur.com/lwGkVo1.png)
