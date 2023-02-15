---
title: "[Typescript] Types Around Events and Refs"
---

# Typing Event Handlers

## Inline Event Handlers

Inline callback function works fine.
- *Type inference* is applied inside of the JSX.
- ```tsx
  const EventComponent: React.FC = () => {
    return (
      <div>
        <input onChange={(e) => console.log(e)} /> // callback function works fine
      </div>
    )
  };

  export default EventComponent;
  ```

## Stand-alone Event Handlers

When defining the callback function outside of the JSX, you have to manually type the event object.
- Typescript does not understand that the event `e` is of `React.ChangeEvent<HTMLInputElement>` type.
- There are many different type of event objects.
- ```tsx
  const EventComponent: React.FC = () => {
    // const onChange = (e) => { console.log(e); } // error('e' has an 'any' type)
    const onChange = (e: React.ChangeEvent<HTMLInputElement>) => { console.log(e); }
    const onDragStart = (e: React.DragEvent<HTMLDivElement>) => { console.log(e); }

    return (
      <div>
        <input onChange={onChange} />
        <div draggable onDragStart={onDragStart}>Drag Me!</div>
      </div>
    )
  };

  export default EventComponent;
  ```

# Typescript with Class Components

Class based components can be defined with typescript as well.
- ```tsx
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

When defining Ref, typescript does not know whether the Ref is assigned to any element yet.
- Ref must be either some element type or `null`.
- ```tsx
  import { useState, useRef } from "react";

  const users = [
    { name: "Sarah", age: 20 },
    { name: "Alex", age: 20 },
    { name: "Michael", age: 20 },
  ]

  const UserSearch: React.FC = () => {
    // const inputRef = useRef(); // error
    // const inputRef = useRef<HTMLInputElement>(); // error
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

## More on Refs

Ref can possibly be `null`.
- Focusing on an element by `useEffect` hook needs extra error handling.
- ```tsx
  import { useState, useRef, useEffect } from "react";
  //...
  const UserSearch: React.FC = () => {
    //...
    useEffect(() => {
      // 'inputRef.current' can possibly be 'null'
      if (!inputRef.current) { return; }
      inputRef.current.focus();
    }, []);
    //...
  }

  export default UserSearch;
  ```
