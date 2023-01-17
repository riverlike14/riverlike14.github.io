---
title: "[React] Some Button Theory"
---

# Overview

## Features

- Make `<Button />` component that can work just like as `<button />`.
- passover all of the props from `<Button />` to the underlying element `<button />`
- different style for different props
- tailwindcss
- `prop-types` library to error check.

## Library

- `prop-types`
- `tailwindcss`
- `classnames`
- `react-icons`

# Some Button Theory

![Buttons](https://i.imgur.com/CBcRwPb.png)

Engineers must use one single button component. How?
1. Use the `Button` component.
2. Don't make `<button />` elements.
3. All buttons will be styled based on the purpose/intent of the button.
4. No custon CSS. Only presets.

![Buttons and explanations](https://i.imgur.com/UvcnN8Z.png)

- Color sets differ from companies.
- Each web page has consistent color design.

# Underlying Elements

![Button component](https://i.imgur.com/GJrthtl.png)

When a component tries to create the exact same plain html equivalent element,
- **Wrapper**: Component
- **Underlying element**: Plaine HTML element

```jsx
// "./Button.js"
const Button = ({ children }) => { // Wrapper
  return (
    <button>{children}</button> // Underlying element
  );
};

export default Button;
```

# The Children Prop

# Props Design

![Button props design](https://i.imgur.com/T7Yt7Er.png)

- `<Button purpose="primary" rounded outline />` works fine.
- But `<Button primary rounded outline />` reads better.

# Validating Props with PropTypes

- `if (primary && secondary) { throw new Error("...") }` works.
- But too many extra code.

`prop-types` javascript library
- Optional
- JS library to validate the props that get passed into your component.
- If someone passes down the incorrect kind of value(number instead of boolean), a warning will appear in console.
- Used to be very popular. Now Typescript does almost the same thing (and more).

```jsx
import PropTypes from "prop-types";

function Card({ title, content, showImage }) {
  // ...
}

Card.propTypes = { // Validation rules
  title: PropTypes.string.isRequired,
  content: PropTypes.string,
  showImage: PropTypes.bool
}
```

- Our case is different.

# PropTypes in Action

```jsx
// "./Button.js"
import PropTypes from "prop-types";

// ...

Button.propTypes = {
  checkVariationValue: ({ primary, secondary, success, warning, danger }) => {
    const count = Number(!!primary) +
      Number(!!secondary) +
      Number(!!success) +
      Number(!!warning) +
      Number(!!danger);

    if (count > 1) {
      return new Error("Only one of primary, secondary, success, warning, danger can be true.");
    }
  },
}

export default Button;
```

- primary, secondary, ... will be `undefined`
- `Number(undefined) = NaN` where as `Number(!!undefined) = 0`

# TailwindCSS

```css
// TailwindCSS exmample
.block {
  display: block;
}
.mr-1 {
  margin-right: 4px;
}
.w-2 {
  width: 8px;
}
.text-white {
  color: white;
}
```

**TailwindCSS** is a library with lots of classNames.
- Each className provides one styling rule.
- However, Tailwind is much harder to read unlike Bulma, Bootstrap, and other css libraries.

Challenges when you use Tailwind:
- Component gets harder to read.
- Too many classNames to look up to apply any styling.
- Some normal CSS features don't work well with Tailwind.

Advantages when you use Tailwind:
- The className soup is going to *force* you to write smaller and more reusable components.

## Installing Tailwind

Follow the instructions on [https://tailwindcss.com/docs/guides/create-react-app](https://tailwindcss.com/docs/guides/create-react-app).
- Tailwind releases new version *extremely frequently*.
- Setup directions might change.

## How to use Tailwind

1. Decide on a new styling rule you want to add.
- Note: Tailwind removes default HTML styles.
2. Go to [https://tailwindcss.com/docs/](https://tailwindcss.com/docs/).
3. Hit `Ctrl + K`(or `command + K`) and search for your styling rule.
4. Add appropriate className to your elements.

# Review on Styling

Our goal: style button element with TailwindCSS.

![Change styling](https://i.imgur.com/MBtxokR.png)

We have to know what styling rules we need to change to get a 'primary' looking button.

CSS Box Model
![CSS Box Model](https://i.imgur.com/qYCs8h0.png)

- Border controlled by "border-width", "border-color"
- Padding controlled by "padding", "background-color"

We need to change
- Padding
- Border width
- Border color
- Background color
- Text color

```jsx
// "./Button.js"
import PropTypes from "prop-types";

const Button = ({
  // ...
}) => {
  return (
    <button className="px-3 py-1.5 border border-blue-600 bg-blue-500 text-white">
      {children}
    </button>
  );
};

// ...

export default Button;
```

# The `ClassNames` Library

- Optional.
- JS library for building up a `className` string based on different values.
- Library is called `classnames`, but prop is `className`.

```js
let bgColor = undefined;
if (primary) {
  bgColor = "bg-blue-500";
}

classNames(bgColor, "px-3", "py-1.5");
```

```js
const primary = true;
const warning = false;

classNames({
  "bg-blue-500": primary,
  "bg-yellow-500": warning
})
```

For eacth key-value pair,
- Is the value truthy?
- If so, add the key to the string I'm building.

# Building Some Variations

# Finalizing the Variations

Conflicting class name -> later classname is going to override the earlier classname

![Buttons results](https://imgur.com/ScF6Qcr.png)

```js
// "./Button.js"
import classNames from "classnames";

const Button = ({ 
  children,
  primary,
  secondary,
  success,
  warning,
  danger,
  outline,
  rounded
}) => {
  const classes = classNames("px-3 py-1.5 border", {
    "border-blue-600 bg-blue-500 text-white": primary,
    "border-gray-900 bg-gray-800 text-white": secondary,
    "border-green-600 bg-green-500 text-white": success,
    "border-yellow-600 bg-yellow-500 text-white": warning,
    "border-red-600 bg-red-500 text-white": danger,
    "rounded-full": rounded,
    "bg-white": outline,
    "text-blue-500": outline && primary,
    "text-gray-900": outline && secondary,
    "text-green-600": outline && success,
    "text-yellow-600": outline && warning,
    "text-red-600": outline && danger,
  });

  return (
    <button className={classes}>
      {children}
    </button>
  );
};

export default Button;
```

# Using Icons in React Projects

Install React-icons

![React-icon version 1](https://imgur.com/amEtBkg.png)

Icon is displayed on top of the text.
- add `flex items-center` in `Button` component.

```jsx
// "./Button.js"
import classNames from "classnames";

const Button = ({ 
  // ...
}) => {
  const classes = classNames("flex items-center px-3 py-1.5 border", {
    // add "flex items-center"
    // ...
  });
};

export default Button;
```

![React-icon version 2](https://imgur.com/c0ym1KL.png)

Icon and text are too close.
- `<SiManjaro className="mr-1">` works, but you have to add it to all of icons.
- When using tailwindCSS, don't have a single place to handle this problem.
  - apply margin when icon is in button
- edit index.css file

```css
/* index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

button > svg {
  margin-right: 5px;
}
```

![React-icon version 3](https://i.imgur.com/2pjhql7.png)

# Issues with Event Handlers

Now, we've got a great Button component.
- Let's tell all other engineers on our project to use our `<Button />` instead of `<button />`.
- Immediate feedback would be "How do I add a click event handler to this `<Button />`?"

possible solution:
- take the `onClick` prop and *pass it through* to the plain button.
- But not a perfect solution...

After some amount time,
- Another feedback would be "How do I add a mouseover event handler to this `<Button />`?"
  - `primary`, `outline`, `children`, ...: Props that our `<Button />` cares about.
  - `onClick`, `onMouseOver`, ...: The rest of the props, probably for the `<button />`?

It is impossible to pass down all the event handlers to the plain button manually.
- We need a better solution.

# Passing Props Through

`...rest` does

```jsx
// "./Button.js"
import classNames from "classnames";

const Button = ({ 
  children,
  primary,
  secondary,
  success,
  warning,
  danger,
  outline,
  rounded,
  ...rest
}) => {
  // ...

  return (
    <button {...rest} className={classes}>
      {children}
    </button>
  );
};

export default Button;
```

# Handling the Special ClassName Case

`<Button className="mb-5" primary />` will not work.
- `<button {...rest} className={classes}/>`
- `classes` will override `{...rest}` which contains `className="mb-5"`.
- Add `rest.className` in the `classNames` function.

```jsx
// "./Button.js"
import classNames from "classnames";

const Button = ({ 
  // ...
  ...rest
}) => {
  const classes = classNames(rest.className, "flex items-center px-3 py-1.5 border", {
    // ...
  });

  return (
    <button {...rest} className={classes}>
      {children}
    </button>
  );
};

export default Button;
```

<!-- ![Handles className props](https://i.imgur.com/VOuypCB.png) -->
