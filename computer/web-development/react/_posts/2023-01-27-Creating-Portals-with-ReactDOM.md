---
title: "[React] Creating Portals with ReactDOM"
---

# Modal Component Overview

Modal window

Open up a modal window
- Display some center panel to a user, and show some important information in there
- Dark background behind the modal window.
- modal window overlay anything else behind it.
- click button or background to close the modal window.

Immediate Tasks
1. Make a `Modal` component
2. Make a `ModalPage` component
3. Add route to `App.js` to show `ModalPage` when a user goes to `/modal`
4. Add a link to the `Sidebar` component

```js
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


```js
// "./components/Sidebar.js"
import Link from "./Link";

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

When modal clicked, content is changing
- Sign that we need state

![where to render button?](https://i.imgur.com/nqvpbru.png)

1. Modal shows a button and owns state
![Modal component design option 1](https://i.imgur.com/pmnE4Tc.png)

Probably not a good idea
- We might want to show a modal *without* a user clicking on a button.

2. Parent owns state to keep track of whether modal should be displayed
![Modal component design option 2](https://i.imgur.com/SRZSzx2.png)
- Parent component can show a modal at anytime for any reason

```jsx
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

3. Better Option
whenever user clicks button inside the modal, or clicks background, want to hide the modal.
- Create some call back function and pass down to the modal.
![Modal component better idea](https://i.imgur.com/MocisBl.png)

# At First Glance, Easy!

Correctly Displaying a Modal is Hard
1. The background of the modal needs to cover *the entire screen*
2. The modal needs to cover up all existing content

Initially, showing the modal will be easy
We'll change our code to simulate a real project and see challenges

![Modal screen shot](https://imgur.com/gjsHEZC.png)

```jsx
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

# We're Lucky it Works At All!

What is `absolute inset-0`?
![TailwindCSS to Real CSS](https://i.imgur.com/7tQbXfv.png)


`position` CSS Property
- `static`
- `relative`
- `absolute`: Puts the element
  - at the top left corner
  - of the closest parent
  - with a position other than `static`
- `fixed`
- `sticky`

className `inset-0`
- If the element is position: absolute
- the element will expand to fill the height and width
- Of the closest parent with a non-static position

At the moment, none of parent elements of modal are positioned.
- Gray background **only working correctly** because there is no "positioned" parent
- If our modal had a **positioned parent**, it would **not fill up the entire screen**.
- Our modal worked just because it happened to have not positioned parent.

- The modal is only working correctly because we don't have a positioned parent
- If we had a positioned parent, it would display incorrectly
- **Real projects use position parents! Sometimes it can't be avoided**

# Fixing the Modal with Portals

![Bad modal design](https://i.imgur.com/i1FZhR7.png)

![React Portal and Modal](https://i.imgur.com/vTB4EFg.png)

- This div will never have a 'positioned' parent
- Modal will be positioned relative to the HTML doc
- **It will always fill the entire screen!**

```jsx
function Modal() {
  return ReactDOM.createPortal(
    <div>
      I'm a modal!
    </div>,
    document.querySelector(".modal-container")
  );
}
```
`ReactDOM.createPortal`
- 1st arg: Tells React to place HTML produced by this component somewhere else
- 2nd arg: Reference to an element in our index.html file

```html
<!-- "../public/index.html" -->
<!DOCTYPE html>
<html lang="en">
  <head>
    ...
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
    <div class="modal-container"></div>
  </body>
</html>
```

```jsx
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
- now our modal works

# Closing the Modal

Pass down click event handler

```jsx
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

```jsx
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

Modal component is not really going to know what component needs to be shown
- Parent will customize both these areas independently
- both children and actionbar must be renderable

![ModalPage and Modal design](https://i.imgur.com/fyQ2aLV.png)

```jsx
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

```jsx
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

![ModalPage sample](https://imgur.com/6I8dMVq.png)

# Additional Styling

CSS styling, homework

```jsx
// "./components/Modal.js"
import ReactDOM from "react-dom";

const Modal = ({ onClose, children, actionBar }) => {
  return ReactDOM.createPortal(
    <div>
      <div onClick={onClose} className="absolute inset-0 bg-gray-300 opacity-80"></div>
      <div className="absolute inset-40 p-10 bg-white">
        <div className="flex flex-col justify-between h-full">
          {children}
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

# One Small Bug

![Small Bug](https://imgur.com/uJNEfX2.png)

Whenever the modal is displayed, we disable scrolling the entire document

# Modal Wrapup

`overflow-hidden` handles the issue
- When model is displayed, add `overflow-hidden` style to the body element
- whenever we remove the modal , remove the style
- `useEffect` hook

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

  return ReactDOM.createPortal(
    <div>
      <div onClick={onClose} className="absolute inset-0 bg-gray-300 opacity-80"></div>
      <div className="absolute inset-40 p-10 bg-white">
        <div className="flex flex-col justify-between h-full">
          {children}
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

Suppose modal button is at the bottom of the modal page
![Wrong Modal rendering location](https://imgur.com/GG86Udh.png)

`absolute` &rarr; `fixed`

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

  return ReactDOM.createPortal(
    <div>
      <div onClick={onClose} className="fixed inset-0 bg-gray-300 opacity-80"></div>
      <div className="fixed inset-40 p-10 bg-white">
        <div className="flex flex-col justify-between h-full">
          {children}
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
