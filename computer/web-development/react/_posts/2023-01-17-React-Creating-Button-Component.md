---
title: "[React] Creating Button Component"
---

# Overview

![Buttons](https://i.imgur.com/CBcRwPb.png)

We are going to learn how wrap `<button />` HTML element.
- Create `<Button />` component that works equivalently as `<button />` element.

## Features

- Pass all of the props from `<Button />` component to the underlying `<button />` element.
- Make props in `<Button />` so that user can apply different style.
- Use TailwindCSS.
- Use prop-types to check errors.
- Use classnames to combine multiple class names.

## Installed Libraries

- `prop-types`
- `tailwindcss`
- `classnames`
- `react-icons`

# Custom `<Button />` Component

Rules with `<Button />` component:
1. Use the `<Button />` component only.
2. Don't make `<button />` elements.
3. All buttons will be styled based on the purpose/intent of the button.
- ![Buttons and explanations](https://i.imgur.com/UvcnN8Z.png)
4. No custom CSS. Only presets.

## Underlying Elements

Create a component that works exactly same as the plain equivalent HTML element.
- **Wrapper**: Component wrapping the plain HTML element.
  - Additional props can be passed to this wrapper.
  - ![Button component](https://i.imgur.com/GJrthtl.png)
- **Underlying element**: The plain HTML element.

```jsx
// "./Button.js"
const Button = ({ children }) => { // Wrapper
  return (
    <button>{children}</button> // Underlying element
  );
};

export default Button;
```

## Props Design

![Button props design](https://i.imgur.com/T7Yt7Er.png)

- `<Button purpose="primary" rounded outline />` does fine.
- But `<Button primary rounded outline />` reads better.

# Validating Props with PropTypes

`<Button />` component does not expect multiple purposes.
- No more than one `primary`, `secondary`, `success`, `warning`, `danger` props can be passed.
- You can type `if (primary && secondary) { throw new Error("...") }; ...` inside the code.
  - But it requires too many extra codes.

## `prop-types` Library

**prop-types** is a Javascript library that validates the props that get passed into a component.

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

- It is optional.
- If someone passes down the incorrect kind of value(number instead of boolean), a warning will appear in console.
- It is used to be very popular.
  - However, [Typescript](https://www.typescriptlang.org) does almost the same thing (and more).

## PropTypes in Action

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

- If not passed down, `primary`, `secondary`, ... will be `undefined`
  - `Number(undefined)` is `NaN`,  whereas `Number(!!undefined)` becomes `0`.

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
2. Go to [https://tailwindcss.com/docs/](https://tailwindcss.com/docs/).
3. Hit `Ctrl + K`(or `command + K`) and search for your styling rule.
4. Add appropriate className to your elements.

# Review on Styling

We want to make a style for the `<Button />` element with TailwindCSS.

![Change styling](https://i.imgur.com/MBtxokR.png)

- Tailwind removes default HTML styles.
- Therefore, we have to figure out what styling rules we need to get a `primary` looking button from scratch.

## CSS Box Model

![CSS Box Model](https://i.imgur.com/qYCs8h0.png)

- Border controlled by `border-width`, `border-color`
- Padding controlled by `padding`, `background-color`

## `<Button />` Style with TailwindCSS

Styles that we need to change for `<Button />` component:
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

# The `Classnames` Library

**Classnames** is a Javascript library for building up a `className` string based on different values.
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

- It is optional.
- Library is called `classnames`, but prop is `className`.
- For each key-value pair, add the key to the string if the value is true.

# Building Variations

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

- When there are conflicting class names, later one is overriding the former one.
  - The text color for `<Button primary outline />` becomes blue.

# Using Icons in React Projects

We can use icons in react projects with **React Icons**.
- Simply visit [https://react-icons.github.io/react-icons/](https://react-icons.github.io/react-icons/) and find icons.

```jsx
import { SiManjaro } from "react-icons/si";
import Button from "./Button";

const App = () => {
  return (
    <div>
      <Button primary>
        <SiManjaro />Hello
      </Button>
      <Button secondary>HELLO</Button>
      <Button success>HELLO</Button>
      <Button warning>HELLO</Button>
      <Button danger>HELLO</Button>
    </div>
  );
};

export default App;
```

## Fixing Icons Style

The icon is displayed on top of the text.
- ![React-icon version 1](https://imgur.com/amEtBkg.png)
- Add `flex items-center` in `<Button />` component.

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

Now, the icon and the text are too close to each other.
- ![React-icon version 2](https://imgur.com/c0ym1KL.png)
- Edit icon as `<SiManjaro className="mr-1">` would work, but you have to add it to all of the icons.
- TailwindCSS doesn't have a single place to fix problems like this:
  - Apply margin for 5px when an icon is in a button.
- We have to edit `index.css` file.

```css
/* index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

button > svg {
  margin-right: 5px;
}
```

Problem solved.
- ![React-icon version 3](https://i.imgur.com/2pjhql7.png)

# Issues with Event Handlers

Now, we've got a `<Button />` component.
- It is time to pass down the event handlers from `<Button />` to the `<button />`.
- However, simply taking the event handler and passing it through to the underlying element is not a good solution.
  - For example, `<button onClick={onClick} ... />` can be a possible solution.
  - But there are so many event handlers besides `onClick`, such as `onMouseEnter` or `onMouseLeave` and so on. We have to deal with all of them.

## Passing Props Through

Fortunately, it is possible to pass down all the event handlers to the plain `<button />`.
- Use `...rest` syntax.

```jsx
// "./Button.js"
import classNames from "classnames";

const Button = ({ 
  // ...
  ...rest // rest = { onClick: ... }
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

# Issues with Special Props Name: `className`

User can pass such as `className="mb-5"` props to the `<Button />` componne.
- However, `<Button className="mb-5" primary />` will not work.
  - Props with the same name will override the previous one.
  - In this case, `{...rest}`, which contains `className="mb-5"`, will be overrided by `className={classes}`.
- You can add `rest.className` to the `classNames` function's parameter to solve this issue.

```jsx
// "./Button.js"
import classNames from "classnames";

const Button = ({ 
  // ...
  ...rest
}) => {
  const classes = classNames(rest.className, "flex items-center px-3 py-1.5 border", {
    // rest.className will be passed to the classes variable.
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
