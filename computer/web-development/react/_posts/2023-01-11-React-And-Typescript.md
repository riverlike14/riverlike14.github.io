---
title: "[Typescript] 리액트와 타입스크립트"
---

# `props`와 인터페이스

- 자식 컴포넌트는 부모 컴포넌트로부터 받을 `props`의 정보를 알고 있어야 함.
- 따라서 `props`의 이름, 자료형 등을 담고 있는 interface를 정의해야 함.
- 자식 컴포넌트에게 정의된 `props`를 전달하고 있는지 검사할 수 있음.
- 자식 컴포넌트에게 올바른 값을 전달하고 있는지 검사할 수 있음.

아래와 같은 방식으로 인터페이스를 만들 수 있음.
```tsx
interface ChidProps {
  color: string,
}

export const Child = ({ color }: ChildProps) => {
  return <div>{color}</div>;
}
```

# 타입스크립트와 리액트 컴포넌트

하지만 위 방법으로는 우리가 리액트 컴포넌트를 만든다는 사실을 타입스크립트가 알지 못함.
- 타입스크립트가 보기에 `Child`는 `ChildProps`자료형의 입력을 받고 JSX를 반환하는 함수로만 인지함.
- 반면 리액트는 `propTypes`, `displayName`, `defaultProps`, `contextTypes` 등과 같은 property를 가질 수 있음.
- 즉, 타입스크립트는 이 사실을 알지 못함. (`Child.displayName`은 존재하지 않으므로 에러)

따라서 아래와 같은 방식으로 타입스크립트에게 우리가 리액트 컴포넌트를 만들고 있다는 사실을 전달하는 것이 바람직.

```tsx
interface ChidProps {
  color: string,
}

export const ChildAsFC: React.FC<ChildProps> = ({ color }) => {
    return <div>{color}</div>;
};
```
- `ChildAsFC`는 리액트 function component.
- `ChildAsFC`는 `propTypes`, `contextTypes` 등과 같은 property를 가질 수 있음.
- `ChildAsFC`는 `ChildProps`꼴의 자료형을 가진 `props`를 받음.

# 인터페이스를 통한 함수 정의

리액트 컴포넌트에 `onClick`등과 같은 함수를 전달하기 위해 인터페이스에 함수의 자료형을 정의해야 함.

```tsx
interface ChidProps {
  color: string,
  onClick: () => void, // 함수의 자료형 정의
}

export const ChildAsFC: React.FC<ChildProps> = ({ color, onClick }) => {
  return (
    <div>
      {color}
      <button onClick={onClick}></button>
    </div>
  )
};
```

# 인터페이스를 통한 `children` 정의

`children`역시 자료형을 선언한 뒤에 사용할 수 있음.
- `export const Child = ({ color, children }: ChildProps) => {` 꼴로 정의하면 인터페이스에 `children`을 정의해야 함
- `export const ChildAsFC: React.FC<ChildProps> = ({ color, children }) => {` 꼴로 정의하면 인터페이스에 `children`을 정의할 필요 없음.

```tsx
interface ChidProps {
  color: string,
  onClick: () => void,
  children?: React.ReactNode, // ChildAsFc에서는 필요 없음
}

export const Child = ({ color, onClick, children }: ChildProps) => {
  return <div>{color}</div>;
};

export const ChildAsFC: React.FC<ChildProps> = ({ color, onClick, children }) => {
  return <div>{color}</div>;
};
```
