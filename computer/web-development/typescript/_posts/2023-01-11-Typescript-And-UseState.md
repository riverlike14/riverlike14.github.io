---
title: "[Typescript] 타입스크립트와 `useState`"
---

# 배열의 자료형

타입스크립트는 배열의 자료형을 정의해줘야 함.
- 특히 `useState`훅에서 배열의 자료형을 정의해야 함.

## 예시

아래에서는 `name`을 입력받고 이 값을 배열에 추가하는 컴포넌트를 구현.
- 여기서 `name`을 `guests`배열에 추가하는 `onClick` 함수를 `<button>`에 구현하려 함.
- 하지만 아래 코드에서 타입스크립트는 `guests` 배열의 자료형을 알지 못함.

```tsx
import { useState } from "react";

const GuestList:React.FC = () => {
  const [name, setName] = useState(""); // name은 string 자료형임을 쉽게 알 수 있음.
  const [guests, setGuests] = useState([]); // guests 배열의 자료형이 정의되지 않음.

  const onClick = () => {
    setName("");
    // setGuests([...guests, name]); // 에러 발생
  }

  return (
    <div>
      <h3>Guest List</h3>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button onClick={onClick}>Add Guest</button>
    </div>
  )
}

export default GuestList;
```

이때 `useState`훅에 입력받은 배열의 자료형을 결정해주면 문제 해결.
- 여기서는 문자열을 저장하는 배열이므로 `useState<string[]>([])`으로 수정.

```tsx
import { useState } from "react";

const GuestList:React.FC = () => {
  const [name, setName] = useState("");
  const [guests, setGuests] = useState<string[]>([]); // guests 배열의 자료형이 정의됨.

  const onClick = () => {
    setName("");
    setGuests([...guests, name]);
  }

  return (
    <div>
      <h3>Guest List</h3>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button onClick={onClick}>Add Guest</button>
    </div>
  )
}

export default GuestList;
```

# 정의되지 않은 초기값

정의되지 않은 변수는 `undefined`이므로 이를 고려해야 함.

## 예시

`users`배열에서 이름을 검색하고 이름이 있으면 관련 정보를 보여주는 컴포넌트 구현.
- 이때 `user`의 자료형을 정해야 하는 문제 발생.
- 만약 이름이 발견되면 `user`의 자료형은 `object`가 됨.
- 만약 이름이 발견되지 않으면 `user`의 자료형은 `undefined`가 됨.

```tsx
import { useState } from "react";

const users = [
  { name: "Sarah", age: 20 },
  { name: "Alex", age: 21 },
  { name: "Michael", age: 22 },
];

const UserSearch: React.FC = () => {
  const [name, setName] = useState("");
  // const [user, setUser] = useState(); // user의 자료형은 어떻게 정의할 것인가.
  const onClick = () => {
    const foundUser = users.find(user => {
      return user.name === name;
    })
    // setUser(foundUser);
  }

  return (
    <div>
      User Search
      <input value={name} onChange={3 => setName(e.target.value)} />
      <button onClick={onClick}>Find User</button>
    </div>
  )
};

export default UserSearch;
```

`user`는 위의 두 가지 자료형을 모두 가질 수 있도록 자료형을 정의해주면 됨.
- `useState<{ name: string, age: number } | undefined>();`으로 정의.

```tsx
import { useState } from "react";

const users = [
  { name: "Sarah", age: 20 },
  { name: "Alex", age: 21 },
  { name: "Michael", age: 22 },
];

const UserSearch: React.FC = () => {
  const [name, setName] = useState("");
  const [user, setUser] = useState<{ name: string, age: number } | undefined>();

  const onClick = () => {
    const foundUser = users.find(user => {
      return user.name === name;
    })
    setUser(foundUser);
  }

  return (
    <div>
      User Search
      <input value={name} onChange={3 => setName(e.target.value)} />
      <button onClick={onClick}>Find User</button>
      <div>
        {user && user.name} // undefined일 경우 고려
        {user && user.age} // undefined일 경우 고려
      </div>
    </div>
  )
};

export default UserSearch;
```
