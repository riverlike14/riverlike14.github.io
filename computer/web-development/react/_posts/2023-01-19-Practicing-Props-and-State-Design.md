---
title: "[React] Practicing Props and State Design"
---

# Component Overview

![Overview](https://imgur.com/mJIJiQx.png)

- Dropdown menu.

## Features

- Design dropdown through state design process.
- Implement controlled component.
- Props naming convention.
- Event capturing, event bubbling.
- `useRef` hook.

## Installed Libraries

- `tailwindcss`
- `react-icons`

# Designing the Props

![Dropdown Design](https://i.imgur.com/3xEEDWw.png)
- Parent component passes down some options to Dropdown component.
- Dropdown then renders the options.
- Parent component may want to know which option is selected.

## Passing Down Both Labels and Values

![Bad Dropdown Design](https://i.imgur.com/xuCMm3K.png)

Passing labels down only is not a good idea.
- Developer wants to take a look at what option user has selected.
- User may have selected `mild` flavor, rather than `"I want mild"`.


![Possibe Design](https://i.imgur.com/Kiop0UF.png)

A list of object with information would be a possible design for the option props.
- Developers can say that user has selected `mild` flavor instead of `"I want mild"`.
- Much easier when handling user-selected option.

# Component Creation

How to make the list appear and disappear over time when user clicked the dropdown?
- Content changes on the screen.
- We need to think about **state** and its design.

## More State Design

State Design Process

**Step 1**

How would a user describe this app step by step?
- Clicking the dropdown.
- List of options appears.
- Clicks an option.
- List of options disappears.
- Item clicked appears in the box.

**Step 2**

Categorize each step as state or event handler.
- Clicking the dropdown &rarr; **Event handler**
- List of options appears &rarr; **State**
- Clicks an option &rarr; **Event handler**
- List of options disappears &rarr; **State**
- Item clicked appears in the box &rarr; **State**

**Step 3**

Group common steps. Remove duplicates. Rewrite descriptions.
- An item can be selected &rarr; **State**
- Menu opens and closes &rarr; **State**
- Clicks an option &rarr; **Event handler**
- Clicking the dropdown &rarr; **Event handler**

**Step 4**

Look at mockup. Remove or simplify parts that aren't changing.

- ![Remove everything that does not change](https://i.imgur.com/rdUhQvK.png)

**Step 5**

Replace remaining elements with text descriptions.
- Menu closed, no option selected.
- Menu open, no option selected.
- Menu closed, an option selected.

**Step 6**

Repeat step 4 and step 5 with a different variation.

**Step 7**

Imagine you have to write a function that returns the text of steps 5 and 6. In addition to your component props, *what other arguments would you need?*

- ```js
  const ops = [
    { label: "I want mild", value: "mild" },
    { label: "I'd like spicy", value: "spicy" },
    { label: "Give me extra spicy!", value: "extra_spicy" },
  ];

  const myFunction = (options, /* ??? */) => {
    //
  };

  myFunction(opts, /* ??? */);
  ```
- Whether the menu is opened or closed.
  - `isOpen` with boolean type.
- Which item is selected?
  - `selected` with Option or null type.

**Step 8**

Decide where each event handler + state will be defined.
- ![State location](https://i.imgur.com/c5GLHIf.png)
- The parent of the dropdown possibly wants to know which item is selected.
  - `selected` belongs to parent component.
- Few components care about whether the dropdown is opened or not.
  - `isOpen` belongs to dropdown component.

## Initial Design of `<App />` and `<Dropdown />` Components.

`<App />` Component:

- ```jsx
  // "./App.js"
  import Dropdown from "./components/Dropdown";

  const App = () => {
    const options = [
      { label: "Red", value: "red" },
      { label: "Green", value: "green" },
      { label: "Blue", value: "blue" },
    ]

    return <Dropdown options={options} />;
  }

  export default App;
  ```

`<Dropdown />` Component:

- ```jsx
  // "./components/Dropdown.js"
  import { useState } from "react";

  const Dropdown = ({ options }) => {
    const [isOpen, setIsOpen] = useState(false);

    const handleClick = () => {
      setIsOpen(currentIsOpen => !currentIsOpen);
      // setIsOpen(!isOpen);
    }

    const handleOptionClick = (option) => {
      setIsOpen(false);
    }

    const renderedOptions = options.map(option => {
      return (
        <div onClick={() => handleOptionClick(option)} key={option.value} >
          {option.label}
        </div>
      );
    })

    return (
      <div>
        <div onClick={handleClick}>Select...</div>
        {isOpen && <div>{renderedOptions}</div>}
      </div>
    )
  };

  export default Dropdown;
  ```
- Functional update form is used.
- `handleOptionClick` event handler is outside of the `map` function.

# Dropdown as a Controlled Component

![Selected as a controlled component](https://i.imgur.com/ZoouUv1.png)

- It is equivalent to handle `<input />` tag and its text.
- We can set "Select..." text as the controlled component.

## Controlled Component Implementation

`<App />` Component:
- ```jsx
  // "./App.js"
  import { useState } from "react";
  import Dropdown from "./components/Dropdown";

  const App = () => {
    const [selection, setSelection] = useState(null);

    const handleSelect = (option) => {
      setSelection(option.label);
    }
    //...
    return <Dropdown options={options} selection={selection} onSelect={handleSelect} />;
  }

  export default App;
  ```
- `<App />` handles selected item with `useState` hook.

`<Dropdown />` Component:
- ```jsx
  // "./components/Dropdown.js"
  import { useState } from "react";

  const Dropdown = ({ options, selection, onSelect }) => {
    //...
    const handleOptionClick = (option) => {
      setIsOpen(false);
      onSelect(option);
    }
    //...
    return (
      <div>
        <div onClick={handleClick}>{selection || "Select..."}</div>
        {isOpen && <div>{renderedOptions}</div>}
      </div>
    )
  };

  export default Dropdown;
  ```
- `<Dropdown />` gets information about which item is selected from its parent component.

## Optional Chaining (`?.`)

If the object is `undefined` or `null`, it returns `undefined` instead of throwing an error.
- Don't have to concern about illegal memory access.
- ```js
  let color = null;
  // console.log(color.length); // error
  console.log(color?.length); // undefined
  ```

We can edit our `Dropdown.js` code as below.
- ```js
  // "./components/Dropdown.js"
  import { useState } from "react";

  const Dropdown = ({ options, selection, onSelect }) => {
    //...
    return (
      <div>
        // Optional chaining is used
        <div onClick={handleClick}>{selection?.label || "Select..."}</div>
        {isOpen && <div>{renderedOptions}</div>}
      </div>
    )
  };

  export default Dropdown;
  ```

# Community Convention with Props Names

Every component we make that shows a "form control" will follow this pattern.

![Bad convention with props name](https://i.imgur.com/yKCuj40.png)

- Extremely common patterns:
  - Call the "current value" prop `value`.
  - Call the "value changed" prop `onChange`.

On the other hand, this is bad naming convention of event handlers.

![Good convention with props name](https://i.imgur.com/RSlRCXO.png)

- "Form control" components, but prop names are different.
  - `onSelect`, `onChange`, `onCheck`, ...
  - Prop names are hard to remember.

Preferred naming convention.
- Although each events are different, call all the event handlers `onChange`.
- Same prop names for all form control components.
  - The prop name is easy to remember.

# Panel Component

Encounters a problem while adding some style.

- `className` is too long.
- Some of the class names are duplicated.
  - `border rounded p-3 shadow bg-white w-full`
  - In other words, some component is being repeated. We have to create it as a distinct component.
- ```jsx
  // "./components/Dropdown.js"
  import { useState } from "react";
  import { FiChevronDown } from "react-icons/fi";

  const Dropdown = ({ options, value, onChange }) => {
    //...
    const renderedOptions = options.map(option => {
      return (
        <div
          className="hover:bg-sky-100 rounded cursor-pointer p-1"
          onClick={() => handleOptionClick(option)}
          key={option.value}
        >
          {option.label}
        </div>
      );
    })

    return (
      <div className="w-48 relative">
        <div
          className="flex justify-between items-center cursor-pointer border rounded p-3 shadow bg-white w-full"
          onClick={handleClick}
        >
          {value?.label || "Select..."}
          <FiChevronDown className="text-lg"/>
        </div>
        {isOpen && <div className="absolute top-full border rounded p-3 shadow bg-white w-full">{renderedOptions}</div>}
      </div>
    )
  };

  export default Dropdown;
  ```

## Panel Component

![Panel componnet with tailwind](https://i.imgur.com/9jhCkGp.png)

Repeat what you do [when you create a button component](https://riverlike14.github.io/computer/web-development/react/React-Creating-Button-Component/#issues-with-passing-props).
- Pass down `className`.
- Pass down all the rest of props, especially event handlers.

## Creating the Reusable Panel

- The Panel component is of little help.
- But it shows user a consistent styling of the web page.
- ```jsx
  // "./components/Panel.js"
  import classNames from "classnames";

  const Panel = ({ children, className, ...rest }) => {
    const finalClassNames = classNames(
      "border rounded p-3 shadow bg-white w-full",
      className
    );

    return (
      <div {...rest} className={finalClassNames}>
        {children}
      </div>
    );
  };

  export default Panel;
  ```
- ```jsx
  // "./components/Dropdown.js"
  import { useState } from "react";
  import { FiChevronDown } from "react-icons/fi";
  import Panel from "./Panel";

  const Dropdown = ({ options, value, onChange }) => {
    //...
    return (
      <div className="w-48 relative">
        <Panel
          className="flex justify-between items-center cursor-pointer" 
          onClick={handleClick}
        >
          {value?.label || "Select..."}
          <FiChevronDown className="text-lg"/>
        </Panel>
        {isOpen && <Panel className="absolute top-full">{renderedOptions}</Panel>}
      </div>
    )
  };

  export default Dropdown;
  ```

# A Challenging Extra Feature

![Dropdown does not close](https://imgur.com/M6l9kkU.png)

A dropdown needs to be closed automatically when user clicks anywhere but the dropdown itself.
- Otherwise, there could be many open dropdowns, which becomes very messy.
- However, it is a very challenging feature.
  - Dropdown ***cannot*** listen to clicks on elements created by Button ***using the onClick technique we've seen***.
- A brand-new technique is needed.

## Document-Wide Click Handlers

![Handle event outside of the component](https://i.imgur.com/GcDHQtL.png)

- `event.target` tells us which element was clicked on.
- `document.addEventListener("click", handleClick);` watches for a click on *any* element.
- ```js
  const handleClick = (event) => {
    console.log(event.target);
  };

  document.addEventListener("click", handleClick);
  ```

## Event Capture and Bubbling

![Event phase](https://i.imgur.com/sSZuFc4.png)

- When an event occurs, browser wants to find event handlers to call.
- Order in which this search occurs is divided into three phases.
  - **Capture phase &rarr; Target phase &rarr; Bubble phase**.
  - Usually Capture phase is ignored.
- But, in this case we have to deal with **Capture phase**.

### Capture phase

- Go to *most parent* of clicked element, see if it has handler.
- Go to *second most parent*...
  - Until the browser meets the clicked element.

### Target phase

- Go to the clicked element, check to see if it has event handler.

### Bubble phase

- Go to *parent* of clicked element, see it has handler.
- Then go to *parent's parent*... etc
  - Until the browser meets the most parent of the clicked element.

## Implementation of Event Capturing

`document.addEventListener` receives three arguments.
- Third argument controls Bubble vs Capture
  - It is `false` by default.
  - Most of the time we only need `false` on the third argument.
- ```js
  // Sets up event handler for the bubbling phase
  document.addEventListener("click", handleClick);

  // Sets up event handler for the bubbling phase
  document.addEventListener("click", handleClick, false);

  // Sets up event handler for the capture phase
  document.addEventListener("click", handleClick, true);
  ```

Putting it all together.
- Get reference to dropdown element.
- Check to see if clicked element is inside dropdown
- ```js
  const dropdown = document.querySelector(".w-48");

  const handleClick = (event) => {
    if (dropdown.contains(event.target)) {
      console.log("Inside dropdown");
    } else {
      console.log("OUTSIDE dropdown");
    }
  }

  document.addEventListener("click", handleClick, true);
  ```

## Why a Capture Phase Handler?

<!-- ![Expect inside dropdown](https://imgur.com/MYm7DPc.png) -->
<!-- ![We get OUTSIDE dropdown](https://imgur.com/F2M6S4g.png) -->

![Event handler running](https://i.imgur.com/vJj0NJa.png)

What we expect from `document.addEventListener("click", handleClick, false)`.
1. User clicks on an option.
2. No capture phase handlers, so browser starts Target then Bubble phase.
3. Sees click event handler on the `option` div. This is the one in our component.
4. React calls the event handler in our component.
5. We update state to close dropdown, but React doesn't rerender yet...
6. Click event handler we manually added to document called.
7. We check to see if click was inside of the dropdown.
8. ...React finally rerenders component.

What really happens in `document.addEventListener("click", handleClick, false)`.
1. User clicks on an option.
2. Browser starts Target then Bubble phase, looking for event handler.
3. Sees click event handler on the `option` div, placed by React.
4. React calls the event handler in our component.
5. We update state to close dropdown, but React doesn't rerender yet...
6. *...React finally rerenders component*.
7. Click event handler we manually added to document called.
8. We check to see if click was inside of the dropdown.

How do we debug this?
- Use `performance.now()`.
- ```jsx
  // "./components/Dropdown.js"
  const Dropdown = ({ options, value, onChange }) => {
    // ...
    window.timeTwo = performance.now(); // When the component rerenders
    const handleOptionClick = (option) => {
      window.timeOne = performance.now();
      setIsOpen(false);
      onChange(option);
    }
    // ...
  }

  const handleClick = (event) {
    window.timeThree = performance.now();
    if (dropdown.contains(event.target)) {
        console.log("Inside dropdown");
    } else {
        console.log("OUTSIDE dropdown");
    }
  }
  ```

# Update Event Listener Through `useEffect` Function

Need to call `document.addEventListener("click", handleClick, true)` *once* when dropdown is rendered.
- This is what happens when second argument of `useEffect` is an *empty array*.
  - It is possible to use when the second argument is not an empty array.
  - But the task flow gets very complicated.
- ```jsx
  // "./components/Dropdown.js"
  import { useState, useEffect } from "react";
  import { FiChevronDown } from "react-icons/fi";
  import Panel from "./Panel";

  const Dropdown = ({ options, value, onChange }) => {
    const [isOpen, setIsOpen] = useState(false);

    useEffect(() => {
      const handler = (event) => {
        console.log(event.target);
      };

      document.addEventListener("click", handler, true);
    }, [])
    //...
  };

  export default Dropdown;
  ```

## Reminder on `useEffect` Cleanup

![Fix useEffect](https://i.imgur.com/jjQkY8p.png)
![Cleaup function](https://i.imgur.com/nn4qhIw.png)

When our component is about to removed from the screen, we tell our listener to stop watching for clicks.
- *Return a clean-up function* in `useEffect` function.
- ```jsx
  // "./components/Dropdown.js"
  import { useState, useEffect } from "react";
  import { FiChevronDown } from "react-icons/fi";
  import Panel from "./Panel";

  const Dropdown = ({ options, value, onChange }) => {
    const [isOpen, setIsOpen] = useState(false);

    useEffect(() => {
      //...
      return () => {
        document.removeEventListener("click", handler);
      };
    }, []);
    //...
  };

  export default Dropdown;
  ```

# Referencing Element with `useRef` Hook

Assumes the first element with className `w-48` is the dropdown.
- ```js
  const dropdown = document.querySelector(".w-48");
  ```
- However, this is not always the case.
- ![Issues when multiple dropdowns](https://i.imgur.com/RKSlsvm.png)

## `useRef` in Action

`useRef` hook.
- Allows a component to get a *reference* to a DOM element that it creates.
- 95% of the time used with DOM elements, but can hold a reference to any value.

`useRef` implementation.
1. Create a ref at the top of your component by calling `useRef`.
2. Assign the ref to a JSX element as a prop called `ref`.
3. Access that DOM element with `ref.current`.

## Checking Click Location

- `divEl` can be `null`.
  - So we have to check whether it is `null` or not.
  - Add `if (!divEl.current) { return; }`.
- ```jsx
  // "./components/Dropdown.js"
  import { useState, useEffect, useRef } from "react";
  import { FiChevronDown } from "react-icons/fi";
  import Panel from "./Panel";

  const Dropdown = ({ options, value, onChange }) => {
    //...
    const divEl = useRef();

    useEffect(() => {
      const handler = (event) => {
        if (!divEl.current) { // handles when divEl is null
          return;
        }

        if (!divEl.current.contains(event.target)) {
          setIsOpen(false);
        }
      };
      //...
    }, []);
    //...
    return (
      <div ref={divEl} className="w-48 relative">
        //...
      </div>
    )
  };

  export default Dropdown;
  ```
