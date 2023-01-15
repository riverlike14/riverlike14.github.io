---
title: "[Typescript] Types Around Events and Refs"
---

# Inline Event Handlers

```tsx
const EventComponent: React.FC = () => {
  return (
    <div>
      <input onChange={(e) => console.log(e)} /> // callback function works fine
    </div>
  )
};

export default EventComponent;
```

```tsx
const EventComponent: React.FC = () => {
  const onChange = (e) => { // error('e' has an 'any' type)
    console.log(e);
  }

  return (
    <div>
      <input onChange={onChange} />
    </div>
  )
};

export default EventComponent;
```

# Typing Standalone Event Handlers

Typescript does not understand that the event `e` is of `React.ChangeEvent<HTMLInputElement>` type.
- type inference is only applied inside of the JSX. when we define the callback function directly inline.
- we need to properly type the event object.

```tsx
const EventComponent: React.FC = () => {
  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log(e);
  }

  return (
    <div>
      <input onChange={onChange} />
    </div>
  )
};

export default EventComponent;
```

# Handling Drag Events Tool

```tsx
const EventComponent: React.FC = () => {
  const onDragStart = (e: React.DragEvent<HTMLDivElement>) => {
    console.log(e);
  }

  return (
    <div>
      <div draggable onDragStart={onDragStart}>Drag Me!</div>
    </div>
  )
};

export default EventComponent;
```

# Typescript with Class Components

```tsx
import React, { Component } from "react";

interface User {
  name: string;
  age: number;
}

interface UserSearchProps {
  users: User[]
}

interface UserSearchState {
  name: string;
  user: User | undefined;
}

class UserSearch extends Component<UserSearchProps> {
  state: UserSearchState = {
    name: "",
    user: undefined,
  };

  onClick = () => {
    const foundUser = this.props.users.find(user => {
      return user.name === this.state.name;
    });
    this.setState({ user: foundUser });
  };

  render() {
    const { name, user } = this.state;

    return (
      <div>
        User Search
        <input value={name} onChange={e => this.setState({ name: e.target.value })} />
        <button onClick={this.onClick}>Find User</button>
        <div>
          {user && user.name}
          {user && user.age}
        </div>
      </div>
    )
  }
}

export default UserSearch;
```

# Applying Types to Refs

```tsx
import { useState, useRef } from "react";

const users = [
  { name: "Sarah", age: 20 },
  { name: "Alex", age: 20 },
  { name: "Michael", age: 20 },
]

const UserSearch: React.FC = () => {
  // const inputRef = useRef();
  // const inputRef = useRef<HTMLInputElement>();
  // Typescript does not know whether this ref is assigned to any
  // element yet.
  const inputRef = useRef<HTMLInputElement | null>(null);
  const [name, setName] = useState("");
  const [user, setUser] = useState<{ name: string, age: number } | undefined>();
  
  const onClick = () => {
    const foundUser = users.find(user => {
      return user.name === name;
    });
    setUser(foundUser);
  };

  return (
    <div>
      User Search
      <input ref={inputRef} value={name} onChange={e => setName(e.target.value)} />
      <button onClick={onClick}>Find User</button>
      <div>
        {user && user.name}
        {user && user.age}
      </div>
    </div>
  )
}

export default UserSearch;
```

# More on Refs

```tsx
import { useState, useRef, useEffect } from "react";

const users = [
  { name: "Sarah", age: 20 },
  { name: "Alex", age: 20 },
  { name: "Michael", age: 20 },
]

const UserSearch: React.FC = () => {
  // const inputRef = useRef();
  // const inputRef = useRef<HTMLInputElement>();
  // Typescript does not know whether this ref is assigned to any
  // element yet.
  const inputRef = useRef<HTMLInputElement | null>(null);
  const [name, setName] = useState("");
  const [user, setUser] = useState<{ name: string, age: number } | undefined>();

  useEffect(() => {
    // 'inputRef.current' can possibly be 'null'
    if (!inputRef.current) {
      return;
    }
    inputRef.current.focus();
  }, []);
  
  const onClick = () => {
    const foundUser = users.find(user => {
      return user.name === name;
    });
    setUser(foundUser);
  };

  return (
    <div>
      User Search
      <input ref={inputRef} value={name} onChange={e => setName(e.target.value)} />
      <button onClick={onClick}>Find User</button>
      <div>
        {user && user.name}
        {user && user.age}
      </div>
    </div>
  )
}

export default UserSearch;
```
