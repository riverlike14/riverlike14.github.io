---
title: "[React] Making Navigation Reusable"
---

# Traditional Browser Navigation

Standard Browser Behavior
- When the browser loads a new HTML document, all existing JS variables and code is dumped
  - Does not really matter for a traditional HTML-focused app
  - Kind of bad for a React app
  - With one request, user instantly sees content on the screen

React version
- If our React app followed traditional navigation ideas, it'd take ay more requests to show basic content

# Theory of Navigation in React

How Navigation Works with React
- User types our address in
  - Always send back the `index.html` file
  - When app loads up, look at address bar and use it to decide what content to show
- User clicks a link or presses "back" button
  - Stop the browser's default page-switching behavior
  - Figure out where the user was trying to go
  - Update content on the screen to trick the user into thinking they swapped pages
  - Update address bar to trick the user into thinking they swapped pages

# Extracting the DropdownPage

![Routing app design](https://i.imgur.com/RY4GHvW.png)

# Answering Cricial Questions

critical question
- When app loads up, look at address bar and use it to decide what content to show
  - How do we look at the address bar? What part of it do we care about?
    - `window.location` object

# The PushState Function

Changing the Address Bar
- `window.location = "http://localhost:3000/dropdown"`
  - Causes a full page refresh
- `window.history.pushState({}, "", "/dropdown")`
  - Updates the address bar but does not cause a refresh

# Handling Link Clicks

```jsx
function Link({ to }) {
  const handleClick = (event) => {
    event.preventDefault();
    // ...
  }
  return <a onClick={handleClick} href={to}>Click</a>
}
```
- Prop `to` describes the path that user will go to if they click this
- `event.preventDefault()` stops the standard navigation
- `onClick={handleClick}` detects a click

# Handling Back:Forward Buttons

When user clicks forward or back
- Window emits a `popstate` event if the user's current url was added by `pushState`

![Window history stack](https://i.imgur.com/Jx2VVZ8.png)

# Navigation Context

![NavigationContext](https://i.imgur.com/qF0eo4f.png)

```jsx
// "./context/navigation.js"
import { createContext } from "react";

const NavigationContext = createContext();

const NavigationProvider = ({ children }) => {
  return (
    <NavigationContext.Provider value={{}} >
      {children}
    </NavigationContext.Provider>
  )
};

export { NavigationProvider };
export default NavigationContext;
```

```jsx
// "./index.js"
import "./index.css";
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import { NavigationProvider } from "./context/navigation"

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <NavigationProvider>
    <App />
  </NavigationProvider>
);
```

# Listening to Forward and Back Clicks

```jsx
// "./context/navigation.js"
import { createContext, useState, useEffect } from "react";

const NavigationContext = createContext();

const NavigationProvider = ({ children }) => {
  const [currentPath, setCurrentPath] = useState(window.location.pathname);

  useEffect(() => {
    const handler = () => {
      setCurrentPath(window.location.pathname);
    };
    window.addEventListener("popstate", handler);

    return () => {
      window.removeEventListener("popstate", handler);
    }
  }, []);

  return (
    <NavigationContext.Provider value={{}} >
      {children}
    </NavigationContext.Provider>
  )
};

export { NavigationProvider };
export default NavigationContext;
```

# Programmatic Navigation

navigate
- Call `pushState` to update address bar
- Update `currentPath`
  - Since `pushState` does not trigger a `popstate` event

```jsx
// "./context/navigation.js"
import { createContext, useState, useEffect } from "react";

const NavigationContext = createContext();

const NavigationProvider = ({ children }) => {
  const [currentPath, setCurrentPath] = useState(window.location.pathname);

  useEffect(() => {
    const handler = () => {
      setCurrentPath(window.location.pathname);
    };
    window.addEventListener("popstate", handler);

    return () => {
      window.removeEventListener("popstate", handler);
    }
  }, []);

  const navigate = (to) => {
    window.history.pushState({}, "", to);
    setCurrentPath(to);
  }

  return (
    <NavigationContext.Provider value={{ currentPath, navigate }} >
      {children}
    </NavigationContext.Provider>
  )
};

export { NavigationProvider };
export default NavigationContext;
```
- No refresh when click back

# A Link Component

- Need `<a />` element that goes to a path in our app? **Use this component**
- Need `<a />` element that goes to another domain? **Use a normal `<a />` element**

```jsx
// "./components/Link.js"
import { useContext } from "react";
import NavigationContext from "../context/navigation";

const Link = ({ to, children }) => {
  const { navigate } = useContext(NavigationContext);

  const handleClick = (event) => {
    event.preventDefault();

    navigate(to);
  }

  return (
    <a onClick={handleClick}>{children}</a>
  );
};

export default Link;
```

# A Route Component

![Route component](https://i.imgur.com/QSg3X2D.png)

```jsx
// "./components/Route.js"
import { useContext } from "react";
import NavigationContext from "../context/navigation";

const Route = ({ path, children }) => {
  const { currentPath } = useContext(NavigationContext);

  if (path === currentPath) {
    return children;
  }

  return null;
};

export default Route;
```

```jsx
// "./App.js"
import Link from "./components/Link";
import Route from "./components/Route";
import AccordionPage from "./pages/AccordionPage";
import DropdownPage from "./pages/DropdownPage";

const App = () => {
  return (
    <div>
      <Link to="/accordion">Go to accordion</Link>
      <Link to="/dropdown">Go to dropdown</Link>
      <div>
        <Route path="/accordion">
          <AccordionPage />
        </Route>
        <Route path="/dropdown">
          <DropdownPage />
        </Route>
      </div>
    </div>
  );
}

export default App;
```

# Handling Control and Command Keys

```jsx
// "./components/Link.js"
import { useContext } from "react";
import NavigationContext from "../context/navigation";

const Link = ({ to, children }) => {
  const { navigate } = useContext(NavigationContext);

  const handleClick = (event) => {
    if (event.metaKey || event.ctrlKey) {
      return;
    }
    event.preventDefault();

    navigate(to);
  }

  return (
    <a href={to} onClick={handleClick}>{children}</a>
  );
};

export default Link;
```

# Link Styling

```jsx
// "./components/Link.js"
import classNames from "classnames";
import { useContext } from "react";
import NavigationContext from "../context/navigation";

const Link = ({ to, children }) => {
  const { navigate } = useContext(NavigationContext);

  const classes = classNames("text-blue-500");

  const handleClick = (event) => {
    if (event.metaKey || event.ctrlKey) {
      return;
    }
    event.preventDefault();

    navigate(to);
  }

  return (
    <a className={classes} href={to} onClick={handleClick}>{children}</a>
  );
};

export default Link;
```

# Custom Navigation Hook

```js
// "./hooks/use-navigation.js"
import { useContext } from "react";
import NavigationContext from "../context/navigation";

const useNavigation = () => {
  return useContext(NavigationContext);
}

export default useNavigation;
```

```jsx
// "./components/Link.js"
import classNames from "classnames";
import useNavigation from "../hooks/use-navigation";

const Link = ({ to, children }) => {
  const { navigate } = useNavigation();

  const classes = classNames("text-blue-500");

  const handleClick = (event) => {
    if (event.metaKey || event.ctrlKey) {
      return;
    }
    event.preventDefault();

    navigate(to);
  }

  return (
    <a className={classes} href={to} onClick={handleClick}>{children}</a>
  );
};

export default Link;
```

```jsx
// "./components/Route.js"
import useNavigation from "../hooks/use-navigation";

const Route = ({ path, children }) => {
  const { currentPath } = useNavigation();

  if (path === currentPath) {
    return children;
  }

  return null;
};

export default Route;
```

# Adding a Sidebar Component

set default page as dropdown

```jsx
// "./App.js"
import Link from "./components/Link";
import Route from "./components/Route";
import AccordionPage from "./pages/AccordionPage";
import DropdownPage from "./pages/DropdownPage";

const App = () => {
  return (
    <div>
      <Link to="/accordion">Go to accordion</Link>
      <Link to="/dropdown">Go to dropdown</Link>
      <div>
        <Route path="/accordion">
          <AccordionPage />
        </Route>
        <Route path="/">
          <DropdownPage />
        </Route>
      </div>
    </div>
  );
}

export default App;
```

```jsx
// "./components/Sidebar.js"
import Link from "./Link";

const Sidebar = () => {
	const links = [
		{ label: "Dropdown", path: "/" },
		{ label: "Accordion", path: "/accordion" },
		{ label: "Buttons", path: "/buttons" },
	];

	const renderedLinks = links.map(link => {
		return <Link key={link.label} to={link.path} >{link.label}</Link>;
	});

	return (
		<div className="sticky top-0 overflow-y-scroll flex flex-col" >
			{renderedLinks}
		</div>
	)
};

export default Sidebar;
```

```jsx
// "./App.js"
import Sidebar from "./components/Sidebar";
import Route from "./components/Route";
import AccordionPage from "./pages/AccordionPage";
import DropdownPage from "./pages/DropdownPage";
import ButtonPage from "./pages/ButtonPage";

const App = () => {
	return (
		<div className="container mx-auto grid grid-cols-6 gap-4 mt-4"> // Set sidebar on the left
			<Sidebar />
			<div className="col-span-5">
				<Route path="/accordion">
					<AccordionPage />
				</Route>
				<Route path="/">
					<DropdownPage />
				</Route>
				<Route path="/">
					<DropdownPage />
				</Route>
				<Route path="/buttons">
					<ButtonPage />
				</Route>
			</div>
		</div>
	);
}

export default App;
```

# Highlighting the Active Link

![Highlight the active link](https://i.imgur.com/5yrF0bb.png)

```jsx
// "./components/Sidebar.js"
import Link from "./Link";

const Sidebar = () => {
	const links = [
		{ label: "Dropdown", path: "/" },
		{ label: "Accordion", path: "/accordion" },
		{ label: "Buttons", path: "/buttons" },
	];

	const renderedLinks = links.map(link => {
		return (
			<Link
				key={link.label} 
				to={link.path} 
				className="mb-3"
				activeClassName="font-bold border-l-4 border-blue-500 pl-2"
			>
				{link.label}
			</Link>
		)
	});

	return (
		<div className="sticky top-0 overflow-y-scroll flex flex-col items-start">
			{renderedLinks}
		</div>
	)
};

export default Sidebar;
```

```jsx
// "./components/Link.js"
import classNames from "classnames";
import useNavigation from "../hooks/use-navigation";

const Link = ({ to, children, className, activeClassName }) => {
	const { navigate, currentPath } = useNavigation();

	const classes = classNames(
		"text-blue-500",
		className,
		currentPath === to && activeClassName
	);

	const handleClick = (event) => {
		if (event.metaKey || event.ctrlKey) {
			return;
		}
		event.preventDefault();

		navigate(to);
	}

	return (
		<a className={classes} href={to} onClick={handleClick}>{children}</a>
	);
};

export default Link;
```
