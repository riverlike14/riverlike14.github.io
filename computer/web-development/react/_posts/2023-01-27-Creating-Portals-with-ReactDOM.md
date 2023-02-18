---
title: "[React] Creating Portals with ReactDOM"
---

# Modal Component Overview

![Modal Preview](https://imgur.com/TndJ5NM.png)

## Features

- Display some center panel to a user, and show some important information in there.
- Dark background behind the modal window.
- Modal window overlay anything else behind it.
- Click button or background to close the modal window.

## Library

- `tailwindCSS`

## Begin with Boilerpaltes

1. Make a `Modal` component.
2. Make a `ModalPage` component.
3. Add route to `App.js` to show `ModalPage` when a user goes to `/modal`.
- ```jsx
  // "./App.js"
  //...
  import ModalPage from "./pages/ModalPage";
  const App = () => {
    return (
          //...
          <Route path="/modal">
            <ModalPage />
          </Route>
          //...
    );
  }
  export default App;
  ```
4. Add a link to the `Sidebar` component.
- ```js
  // "./components/Sidebar.js"
  //...
  const Sidebar = () => {
    const links = [
      //...
      { label: "Modal", path: "/modal" },
    ];
    //...
  };
  export default Sidebar;
  ```

# Toggling Visibility

When the modal is clicked, the content is going to change.
- We need to use `useState` hook.
- Display the Modal window when a user clicks a button.

Where should the Modal component be displayed?
- ![Modal component design option 1](https://i.imgur.com/pmnE4Tc.png)
   OR ![Modal component design option 2](https://i.imgur.com/SRZSzx2.png)
- Option 1: Modal shows a button and owns state.
  - Probably not a good idea.
  - We might want to show a modal *without* a user clicking on a button.
- Option 2: Parent owns state to keep track of whether modal should be displayed.
  - Parent component can show a modal at anytime for any reason.
  - ```jsx
    // "./pages/ModalPage.js"
    import { useState } from "react";
    import Modal from "../components/Modal";
    import Button from "../components/Button";

    const ModalPage = () => {
      const [showModal, setShowModal] = useState(false);

      const handleClick = () => {
        setShowModal(true);
      }

      return (
        <div>
          <Button onClick={handleClick} primary>Open Modal</Button>
          { showModal &&  <Modal /> }
        </div>
      )
    }

    export default ModalPage;
    ```
  - ```jsx
    // "./components/Modal.js"
    const Modal = () => {
      return (
        <div>
          <div className="absolute inset-0 bg-gray-300 opacity-80"></div>
          <div className="absolute inset-40 p-10 bg-white">
            I am a modal!
          </div>
        </div>
      )
    }

    export default Modal;
    ```


## Modal with CSS Only Will Not Work

![Modal screen shot](https://imgur.com/gjsHEZC.png)
- It seems that we successfully made a modal component.
- However, it will turn out that we did not.

No matter what the parent comonent is, our modal component has to be rendered without any error.
1. The background of the modal needs to cover *the entire screen*.
2. The modal needs to cover up all existing content.

**What is `absolute inset-0`?**

![TailwindCSS to Real CSS](https://i.imgur.com/7tQbXfv.png)

`positions` property.
- `static`, `relative`, `absolute`, `fixed`, `sticky`.
- `absolute`: Puts the element at the top left corner of the closest parent with a position other than `static`.

`inset-0` property.
- If the element is position: absolute
- the element will expand to fill the height and width
- Of the closest parent with a non-static position

**Why our modal worked correctly?**
- Gray background working correctly **only because there is no "positioned" parent.**
- If our modal had a **positioned parent**, it would **not fill up the entire screen.**
- *Our modal worked just because it happened to have not positioned parent.*
- **Real projects use position parents! Sometimes it can't be avoided**

## Fixing the Modal with Portals

![Bad modal design](https://i.imgur.com/i1FZhR7.png)
- It is higly likely that there are some positioned elements in the `<App />` component.
- We have to avoid this structure.

![React Portal and Modal](https://i.imgur.com/vTB4EFg.png)
- We make another `<div />` element outside of the `<App />` component.
- This div will never have a *positioned* parent
- Modal will be positioned relative to the HTML document.
- **It will always fill the entire screen!**

Use **`ReactDOM.createPortal()`** to make the `<div />` element.
- 1st arg: Tells React to place HTML produced by this component somewhere else.
- 2nd arg: Reference to an element in our `index.html` file.
- ```html
  <!-- "../public/index.html" -->
  <!DOCTYPE html>
  <html lang="en">
    <head>
      ...
    </head>
    <body>
      <noscript>You need to enable JavaScript to run this app.</noscript>
      <div id="root"></div>
      <div class="modal-container"></div> <!-- Add this line -->
    </body>
  </html>
  ```
- ```jsx
  // "./components/Modal.js"
  import ReactDOM from "react-dom";

  const Modal = () => {
    return ReactDOM.createPortal(
      <div>
        <div className="absolute inset-0 bg-gray-300 opacity-80"></div>
        <div className="absolute inset-40 p-10 bg-white">
          I am a modal!
        </div>
      </div>,
      document.querySelector(".modal-container")
    );
  };

  export default Modal;
  ```

## Closing the Modal

![Modal component better idea](https://i.imgur.com/MocisBl.png)
- Whenever user clicks button inside the modal, or clicks background, want to hide the modal.
- Simply create some call back function and pass down to the modal.
- ```jsx
  // "./pages/ModalPage.js"
  import { useState } from "react";
  import Modal from "../components/Modal";
  import Button from "../components/Button";

  const ModalPage = () => {
    const [showModal, setShowModal] = useState(false);

    const handleClick = () => {
      setShowModal(true);
    };

    const handleClose = () => {
      setShowModal(false);
    };

    return (
      <div>
        <Button onClick={handleClick} primary>Open Modal</Button>
        { showModal &&  <Modal onClose={handleClose} /> }
      </div>
    )
  }

  export default ModalPage;
  ```
- ```jsx
  // "./components/Modal.js"
  import ReactDOM from "react-dom";

  const Modal = ({ onClose }) => {
    return ReactDOM.createPortal(
      <div>
        <div onClick={onClose} className="absolute inset-0 bg-gray-300 opacity-80"></div>
        <div className="absolute inset-40 p-10 bg-white">
          I am a modal!
        </div>
      </div>,
      document.querySelector(".modal-container")
    );
  };

  export default Modal;
  ```

# Customizing the Modal

Modal component is not really going to know what component needs to be shown.

## Props of the Modal

![ModalPage and Modal design](https://i.imgur.com/fyQ2aLV.png)

- Parent will customize both these areas independently.
- Both `children` and `actionbar` must be renderable.
- ```jsx
  // "./pages/ModalPage.js"
  //...
  const ModalPage = () => {
    //...
    const actionBar = (
      <div>
        <Button onClick={handleClose} primary>I Accept</Button>
      </div>
    );

    const modal = (
      <Modal onClose={handleClose} actionBar={actionBar} >
        <p>
          Here is an important agreement for you to accept
        </p>
      </Modal>
    );

    return (
      <div>
        <Button onClick={handleClick} primary>Open Modal</Button>
        { showModal &&  modal }
      </div>
    )
  }

  export default ModalPage;
  ```
- ```jsx
  // "./components/Modal.js"
  import ReactDOM from "react-dom";

  const Modal = ({ onClose, children, actionBar }) => {
    return ReactDOM.createPortal(
      <div>
        <div onClick={onClose} className="absolute inset-0 bg-gray-300 opacity-80"></div>
        <div className="absolute inset-40 p-10 bg-white">
          {children}
          {actionBar}
        </div>
      </div>,
      document.querySelector(".modal-container")
    );
  };

  export default Modal;
  ```

## Additional Styling

Add some CSS styling.
- ```jsx
  // "./components/Modal.js"
  import ReactDOM from "react-dom";

  const Modal = ({ children, onClose, actionBar }) => {
    return ReactDOM.createPortal(
      <div>
        <div onClick={onClose} className="absolute inset-0 bg-gray-300 opacity-80"></div>
        <div className="absolute inset-40 h-80 p-10 bg-white rounded-lg">
          <div className="flex flex-col justify-between h-full">
            <div className="flex justify-center">
              <h1 className="text-xl font-bold">Modal Header</h1>
            </div>
            <hr />
            {children}
            <hr />
            <div className="flex justify-end">
              {actionBar}
            </div>
          </div>
        </div>
      </div>,
      document.querySelector(".modal-container")
    );
  };

  export default Modal;
  ```

# Some Bugs

## Disable Scrolling

![Small Bug](https://imgur.com/uJNEfX2.png)

Whenever the modal is displayed, we have to disable scrolling the entire document.
- When modal is displayed, add `overflow-hidden` style to the body element.
- whenever we remove the modal, remove the style.
  - `useEffect` hook.

```jsx
// "./components/Modal.js"
import ReactDOM from "react-dom";
import { useEffect } from "react";

const Modal = ({ children, onClose, actionBar }) => {
  useEffect(() => {
    document.body.classList.add("overflow-hidden");
    return () => {
      document.body.classList.remove("overflow-hidden");
    };
  }, []);
  //...
};

export default Modal;
```

## Modal Button Position

![Wrong Modal rendering location](https://imgur.com/GG86Udh.png)

Modal button can be at the bottom of the modal page.
- Change `absolute` to `fixed`.
- ```jsx
  // "./components/Modal.js"
  //...
  const Modal = ({ children, onClose, actionBar }) => {
    //...
    return ReactDOM.createPortal(
      <div>
        <div onClick={onClose} className="fixed inset-0 bg-gray-300 opacity-80"></div>
        <div className="fixed inset-40 h-80 p-10 bg-white rounded-lg">
          /* ... */
        </div>
      </div>,
      document.querySelector(".modal-container")
    );
  };

  export default Modal;
  ```
