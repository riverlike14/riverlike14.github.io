---
title: "[React] State Design Process and Accordion Component"
---

# App Overview

![App Preview](https://imgur.com/SXBRkK2.png)

- Accordion.
- When a header is clicked, it expands its contents and collapses all the others'.

## Features

- Learn how to organize project files.
- Create an accordion component.
- Learn about state design process.
- Handles event handler in `map` function.
- Functional version of update states.

## Library

- `react-icons`
- `tailwindcss`

# Project Organization

![Page and components](https://i.imgur.com/Zv5WkPS.png)

How to organize different files.
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
  - This type of organization design is preferred.

## Tips about File/Folder Organization

- Do not worry about this too much.
- 99% of professional prjects - a senior engineer is going to decide for you.
- On any large project, you'll end up using search to find files, rather than manual navigation.
- Having a bunch of files in a folder is not inherently bad.


# Component Overview

Let's design the component's structure.

## Component Structure

![Components of an accordion](https://i.imgur.com/bmqcewW.png)
![Accordion component](https://i.imgur.com/4iUseYP.png)

- Accordion consists of labels and contents.
- Accordion gets data from the `App` component.
  - Each object would be look like `{ id:, label: , content:  }`

## Component Setup

Simple boilerplates of `App.js` and `Accordion.js`.

`./App.js`
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

`./Accordion.js`
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

**Step 1**

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

**Step 2**

Categorize each step as state or event handler.
- Clicked on third section. &rarr; **event handler**
- First section collapsed. &rarr; **state**
- Third section expanded. &rarr; **state**
- Clicked on second section. &rarr; **event handler**
- Third section collapsed. &rarr; **state**
- Second section expanded. &rarr; **state**

**Step 3**

Group common steps. Remove duplicates. Rewrite descriptions.
- Clicked on a section header. &rarr; **event handler**
- One section is expanded, all others are collapsed. &rarr; **state**

**Step 4**
Look at mockup. Remove or simplify parts that aren't changing.

- Some parts are not changing when user clicks
  - Section title never change.
  - Text in content area is not changing.

**Step 5**

Replace remaining elements with text descriptions

- ![Step 5](https://i.imgur.com/5p235Zs.png)
- Imagine that you tell to some other engineer who cannot see your screen, what the UI currently looks like.

**Step 6**

Repeat step 4 and step 5 with a different variation.

**Step 7**

Imagine you have to write a function that returns the text of steps 5 and 6. In addition to your component props, *what other arguments would you need?*

- Notes on types of state
  - State can be a number, boolean, string, array or object.
  - ***Ideally, avoid arrays/objects***.
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

**Step 8**

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

## Design Result

![Final Design](https://i.imgur.com/kVfgB1l.png)

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

Longhand version:
- Use when handler has more than one lines of code.
- Slightly harder to use with lists.
- ```jsx
  function ProductShow() {
    const handleClick = () => {
      console.log("Clicked!");
    }

    return <div onClick={handleClick}></div>
  }
  ```

Shorthand version:
- Use when handler has 1 line of code.
- Can make JSX harder to read.
- ```jsx
  function ProductShow() {
    return <div onClick={() => console.log("Clicked!")}></div>
  }
  ```

We mix both versions.
- Unfortunately it is still hard to read.
- Note that each function is totally different:
  - `() => setExpandedIndex(0)`
  - `() => setExpandedIndex(1)`
  - `() => setExpandedIndex(2)`
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

## Variation on Event Handlers

First, we can define event handlers inside the `map` function.
- It is not wrong, does fine.
- However, adding logics inside `map` function makes the wrapping function much harder to read and understand.
  - If `handleClick` has many lines, or if there are many event handlers inside the mapping function, it becomes really hard to read and understand.
- ```jsx
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
    // ...
  };

  export default Accordion;
  ```

We can define event handler outside the `map` function.
- The wrapping function becomes much more easy to read and understand.
- Useful when we use `map` function and have to define event handlers.
- Usually event handlers receive `event` object for the first argument.
  - `<div onClick={handleClick} />` runs `handleClick(event)` when clicked.
- ```jsx
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
    // ...
  };

  export default Accordion;
  ```

# Displaying Icons

Use `react-icons`.
- Visit the [page](https://react-icons.github.io/react-icons/).
- Import library which contains the icon that you want to use.

## Conditional Icon Rendering

We can use ternary expression for conditional rendering.
- ```jsx
  const icon = (
    <span>
      {isExpanded ? <RxDoubleArrowDown /> : <RxDoubleArrowLeft />}
    </span>
  );
  ```

# Wrapping Up

Two things are left.
- Add styling.
- Set accordion collapsed as a default state.

## Adding Styling

Add some styling with TailwindCSS at this point.
- ```jsx
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

## Toggling Panel Collapse

Accordion is collapsed as a default state.
- Set `expandedIndex` as -1 so that no index of panel matches.
- ```jsx
  // "./components/Accordion.js"
  // ...
  const Accordion = ({ items }) => {
    const [expandedIndex, setExpandedIndex] = useState(-1);

    const handleClick = (nextIndex) => {
      if (expandedIndex === nextIndex) {
        setExpandedIndex(-1);
      } else {
        setExpandedIndex(nextIndex);
      }
    }
    // ...
  };

  export default Accordion;
  ```

# Delayed State Updates

![Double clicks not working](https://imgur.com/iEeSGhK.png)

There can be a tiny bug when using `useState` hook.
- When `$0.click();`, the first content hides.
  - `$0` refers to the first div.
- However, two times of `$0.click()` at the same time will not open the content again.

## What We Expected

1. `expandedIndex === 0` at first.
- First panel is expanded.
2. User clicks first header(`index === 0`).
- Event handlers executed.
- Because `expandedIndex === index`, we call `setExpandedIndex(-1)`.
- Component rerenders, `expandedIndex === -1`, first section is collapsed.
3. User clicks first header again(`index === 0`).
- Event handler executed.
- First section is expanded.

## What Actually Happens

1. `expandedIndex === 0` at first.
- First panel is expanded.
2. User clicks first header(`index === 0`).
- Event handlers executed.
- Because `expandedIndex === index`, we call `setExpandedIndex(-1)`.
- ~~Component rerenders, `expandedIndex === -1`, first section is collapsed.~~\\
  React: Oh, you want to update state... yeah, I'll get around to it in the future...
3. User clicks first header again(`index === 0`).
- Event handler executed.
  - `expandedIndex` hasn't been updated yet!
  - Because `expandedIndex === index`, we call `setExpandedIndex(-1)`.
  - React: Oh, you want to update state... yeah, I'll get around to it in the future...\\
    (Try to `console.log(expandedIndex)` in `handleClick` if you want to check.)
  - Time passes...
  - React: Guess I'll finally do that state update.
  - **`expandedIndex === -1`.**
- ~~First section is expanded.~~\\
  First section is collapsed.


## Functional State Updates

Option 1.
- Get React to process state updates instantly.
  - React is assuming that there might be some other piece of states to update more or less at the same time.
  - Little delay is refer to fetching. React allows us to several updates. React then process this updates.
- Possible solution, but our app is going to be a little bit slower.

Option 2.
- Get access to the most up to date value of `expandedIndex` in `handleClick`.
- At `setExpandedIndex(-1)`, somewhere, react queued up this instruction and is going to process this instruction at some point of time.
- Our app is going to adopt this option.

Let's compare simple version and functional version of updating states.

**Simple version**
- Use if new state value does not depend on old state value.
- ```jsx
  const handleClick = () => {
    setCounter(10);
  }
  ```

**Functional version**
- Use if new state value depends on old state value.
- Technically it becomes an issue *only if state updates occur really quickly...*
  - You can get pretty far only using the simple version.
- ```jsx
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

## Final Patch

![Result](https://imgur.com/lwGkVo1.png)

The first panel expands even if user clicks two times instantly.
- ```jsx
  // "./components/Accordion.js"
  // ...
  const Accordion = ({ items }) => {
    const [expandedIndex, setExpandedIndex] = useState(-1);

    const handleClick = (nextIndex) => {
      setExpandedIndex(currentExpandedIndex => {
        if (currentExpandedIndex === nextIndex) {
          return -1;
        } else {
          return nextIndex;
        }
      })
    }
    // ...
  };

  export default Accordion;
  ```
