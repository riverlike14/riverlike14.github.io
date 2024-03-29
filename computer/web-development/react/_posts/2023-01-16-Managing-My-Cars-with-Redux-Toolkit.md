---
title: "[React] Managing My Cars with Redux Toolkit"
---
# App Overview

![App Preview](https://imgur.com/70DHFAP.png)

## Features

- Handles two slices.
- Renders searched items.

## Installed Libraries

- `@reduxjs/toolkit`
- `react-redux`
- `bulma`

## Component Design

![App Component Preview](https://i.imgur.com/NcxjMmu.png)

- `CarForm`: Take the car name and the car value and add it in to the list of cars.
- `CarSearch`: Update search term whenever user types in.
- `CarList`: Show the list of cars matching with the search term.
- `CarValue`: Sum of the total value of cars in the current list.


# Redux Store Design

1. Identify what state exists in the app.
2. Identify how that state changes over time.
3. Group together common pieces of state.
4. Create a slice for each group.

## States In Redux Store

This app needs the following states.
- `name`: String
- `cost`: Number
- `searchTerm`: String
- `cars`: Array of `{ id, name, cost }`
- `totalCost`: Number
- `matchedCars`: ???

### Derived State

- However, we can calculate the following states with existing state.
  - `totalCost`
  - `matchedCars`
- Therefore, we don't need `totalCost` and `matchCars`.
  - We only need `name`, `cost`, `searchTerm` and `cars` states.

## Actions In Redux Store

States and their corresponding actions.
- `name` &rarr; change name
- `cost` &rarr; change cost
- `searchTerm` &rarr; change search term
- `cars` &rarr; add car, remove car

## Final Redux Store Design

![Redux State Design](https://i.imgur.com/0mf7ai6.png)

Categorize states and actions into their role.
- `name` and `cost` are related to editing the list of cars.
- `searchTerm` and `cars` are related to showing the list of cars.


# Creating the Store

Here, we focus on Redux side.

## Form Slice

- `name` &rarr; `changeName`
- `cost` &rarr; `changeCost`
- ```jsx
  // "./store/slices/formSlice.js"
  import { createSlice } from "@reduxjs/toolkit";

  const formSlice = createSlice({
    name: "form",
    initialState: {
      name: "",
      cost: 0
    },
    reducers: {
      changeName(state, action) {
        state.name = action.payload;
      },
      changeCost(state, action) {
        state.cost = action.payload;
      }
    }
  })

  export const { changeName, changeCost } = formSlice.actions;
  export const formReducer = formSlice.reducer;
  ```

## Cars Slice

- `searchTerm` &rarr; `changeTerm`
- `cars` &rarr; `addCar`, `removeCar`
- `nanoid`: redux toolkit gives randomly generated id.
- ```jsx
  // "./store/slices/carsSlice.js"
  import { createSlice, nanoid } from "@reduxjs/toolkit";

  const carsSlice = createSlice({
    name: "cars",
    initialState: {
      searchTerm: "",
      cars: []
    },
    reducers: {
      changeSearchTerm(state, action) {
        state.searchTerm = action.payload;
      },
      addCar(state, action) {
        state.cars.push({
          id: nanoid(), // Math.random() works fine
          name: action.payload.name,
          cost: action.payload.cost
        })
      },
      removeCar(state, action) {
        const updated = state.cars.filter(car => {
          return car.id !== action.payload;
        })
        state.cars = updated;
      }
    }
  });

  export const { changeSearchTerm, addCar, removeCar } = carsSlice.actions;
  export const carsReducer = carsSlice.reducer;
  ```

## Creating the Store

`./store/index.js` file
- ```jsx
  // "./store/index.js"
  import { configureStore } from "@reduxjs/toolkit";
  import { carsReducer, addCar, removeCar, changeSearchTerm } from "./slices/carsSlice";
  import { formReducer, changeName, changeCost } from "./slices/formSlice";

  const store = configureStore({
    reducer: {
      cars: carsReducer,
      form: formReducer
    }
  });

  export {
    store,
    changeName,
    changeCost,
    addCar,
    removeCar,
    changeSearchTerm
  };
  ```

`./index.js` file
- ```jsx
  // "./index.js"
  import React from 'react';
  import ReactDOM from 'react-dom/client';
  import { Provider } from "react-redux";
  import { store } from "./store";
  import App from './App';

  const root = ReactDOM.createRoot(document.getElementById('root'));
  root.render(
    <Provider store={store}>
      <App />
    </Provider>
  );
  ```

# Form Values to Update State

Now we focus on React side, especially handling car's name and cost elements.
- It is very similar to [handling controlled components](https://riverlike14.github.io/computer/web-development/react/Using-an-API-with-React/#handling-input-elements).

## Basic Process of Changing and Accessing State

**Changing State Process**

![Redux Changing State Design](https://i.imgur.com/nCWFUUT.png)
1. Add a reducer to one of your slices that changes state in some particular way.
2. Export the action creator that the slice automatically creates.
3. Find the component that you want to dispatch from.
4. Import the action creator function and `useDispatch` from react-redux.
5. Call the `useDispatch` hook to get access to the dispatch function.
6. When the user does something, call the action creator to get an action, then dispatch it.

**Accessing State Process**

![Redux Accessing State Desigin](https://i.imgur.com/TJlSApq.png)
1. Find the component that needs to access some state.
2. Import the `useSelector` hook from react-redux.
3. Call the hook, passing in a selector function.
4. Use the state! Anytime state changes, the component will automatically rerender.

## Car's Form Value Design

![Tentative Results](https://imgur.com/JariskZ.png)

- `handleNameChange`
  - When user changes name in the form, `handleNameChange` dispatches the change.
  - Import `changeName` action creator.
  - Receive `name` value through `useSelector` hook.
- `handleCostChange`
  - When user changes cost in the form, `handleCostChange` dispatches the change.
  - Import `changeCost` action creator.
  - Receive `cost` value through `useSelector` hook.
- `handleSubmit`
  - Dispatch the form when submitted.
  - Import `addCar`.
- ```jsx
  // "./components/CarForm.js"
  import { useDispatch, useSelector } from "react-redux";
  import { changeName, changeCost, addCar } from "../store";

  const CarForm = () => {
    const dispatch = useDispatch();
    const { name, cost } = useSelector(state => {
      return { 
        name: state.form.name,
        cost: state.form.cost
      }
    });

    const handleNameChange = (event) => {
      dispatch(changeName(event.target.value));
    };

    const handleCostChange = (event) => {
      const carCost = parseInt(event.target.value) || 0;
      dispatch(changeCost(carCost));
    };

    const handleSubmit = (event) => {
      event.preventDefault();
      dispatch(addCar({ name: name, cost: cost }));
    };

    return (
      <div className="car-form panel">
        <h4 className="subtitle is-3">Add Car</h4>
        <form onSubmit={handleSubmit}>
          <div className="field-group">
            <div className="field">
              <label className="label">Name</label>
              <input
                className="input is-expanded"
                value={name}
                onChange={handleNameChange}
              />
            </div>
            <div className="field">
              <label className="label">Cost</label>
              <input
                className="input is-expanded"
                value={cost || ''}
                onChange={handleCostChange}
                type="number"
              />
            </div>
          </div>
          <div className="field">
            <button className="button is-link">Submit</button>
          </div>
        </form>
      </div>
    );
  };

  export default CarForm;
  ```

# Rendering List of Cars and Additional Functions

It is time to render list of cars slice.
- CarList component must be able to add and delete a car from the record.

## Awkward Double Keys

Before we render list of cars, we run into a tedious naming problem that will bother you.
- ![State Object in the Store](https://i.imgur.com/1MWMj0v.png)
- Cars slice has two states, `searchTerm` and `cars`.
  - The variable name `state.cars.cars` is awkward.
- Change `cars` in `cars` slice into a more general name, `data`.
- ```js
  // "./store/slices/carsSlice.js"
  import { createSlice, nanoid } from "@reduxjs/toolkit";

  const carsSlice = createSlice({
    name: "cars",
    initialState: {
      searchTerm: "",
      data: [] // More general variable name.
    },
    // ...
  });

  export const { changeSearchTerm, addCar, removeCar } = carsSlice.actions;
  export const carsReducer = carsSlice.reducer;
  ```

## CarList Component Design

![Tentative Result 2](https://imgur.com/rK6o4Aa.png)

- `renderedCars`
  - Render list the records of cars.
- `handleCarDelete`
  - Remove a car from the records.
  - Import `removeCar` action creator.

- ```jsx
  // "./components/CarList.js"
  import { useSelector, useDispatch } from "react-redux";
  import { removeCar } from "../store";

  const CarList = () => {
    const dispatch = useDispatch();

    const cars = useSelector(state => {
      return state.cars.data;
    });

    const handleCarDelete = (car) => {
      dispatch(removeCar(car.id));
    }

    const renderedCars = cars.map(car => {
      return (
        <div key={car.id} className="panel">
          <p>
            {car.name} - ${car.cost}
          </p>
          <button
            className="button is-danger"
            onClick={() => handleCarDelete(car)}
          >
            Delete
          </button>
        </div>
      )
    })

    return (
      <div className="car-list">
        {renderedCars}
        <hr />
      </div>
    );
  };

  export default CarList;
  ```

# Form Reset on Submission

When we submit the new car, the `name` and the `cost` still remain.
- Want to reset the values whenever user submitted the form.
- Calling `changeName('')` and `changeCost('')` when submitted is not a good solution.
  - Calling dispatch multiple times in a single event handler is a sign that we can improve this.

## Extra Reducers technique

![Refactor Actions in Reducers](https://i.imgur.com/YQQDmzh.png)
- Tells Form combined reducer that *it also needs to watch* `cars/addCar` *action type.*
- Whenever it sees this type, reset name and cost.

## Add `extraReducers` in Form Slice

- Import `addCar` so that `formSlice` can watch this action creator.
- ```js
  // "./store/slices/formSlice.js"
  import { createSlice } from "@reduxjs/toolkit";
  import { addCar } from "./carsSlice"; // formSlice needs to watch this.

  const formSlice = createSlice({
    name: "form",
    // ...
    extraReducers(builder) {
      builder.addCase(addCar, (state, action) => {
        state.name = "";
        state.cost = 0;
      });
    }
  });

  export const { changeName, changeCost } = formSlice.actions;
  export const formReducer = formSlice.reducer;
  ```

# Adding a Searching Input

Create a boiler plate of car search bar.
- When user changes search term, `handleSearchTermChange` dispatches the change.
- ```jsx
  // "./components/CarSearch.js"
  import { useDispatch, useSelector } from "react-redux";
  import { changeSearchTerm } from "../store";

  const CarSearch = () => {
    const dispatch = useDispatch();
    const searchTerm = useSelector((state) => {
      return state.cars.searchTerm;
    });

    const handleSearchTermChange = (event) => {
      dispatch(changeSearchTerm(event.target.value));
    };

    return (
      <div className="list-header">
        <h3 className="title is-3">My Cars</h3>
        <div className="search field is-horizontal">
          <label className="label">Search</label>
          <input
            className="input"
            value={searchTerm}
            onChange={handleSearchTermChange}
          />
        </div>
      </div>
    );
  };

  export default CarSearch;
  ```

## Derived State in useSelector

![Derived state filteredCars](https://i.imgur.com/PmTdEy9.png)

- `CarList` only renders a list of cars with matching names.
  - Combine `cars.data` with `cars.searchTerm` to derive `filteredCars`.
- ```jsx
  // "./components/CarList.js"

  // ...
  const CarList = () => {
    const dispatch = useDispatch();

    const cars = useSelector(({ cars: { data, searchTerm } }) => {
      return data.filter((car) => {
        return car.name.toLowerCase().includes(searchTerm.toLowerCase());
      })
    });
    // ...
  };

  export default CarList;
  ```

# Wrapping-Up

- Render total car cost on the screen.
- Highlight(make bold) cars with matching name in the form.

## Total Car Cost

`reduce`: Javascript-style way to sum up.
- 0: Initial value of acc(umulator).
- [More details on `reduce` in here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce).
- ```jsx
  // "./components/CarValue.js"
  import { useSelector } from "react-redux";

  const CarValue = () => {
    const totalCost = useSelector(({ cars: { data, searchTerm } }) => {
      const filteredCars = data.filter((car) => 
        car.name.toLowerCase().includes(searchTerm.toLowerCase())
      );
      return filteredCars.reduce((acc, car) => acc + car.cost, 0);
      /*
        let cost = 0;
        for (let car of filteredCars) {
          cost += car.cost;
        }
        return cost;
      */
    })

    return (
      <div className="car-value">
        Total Cost: ${totalCost}
      </div>
    );
  };

  export default CarValue;
  ```

## Highlighting Existing Cars

CarList highlights car elements with matching name.
- ![Bad data model](https://i.imgur.com/OiUcZ3W.png)
  - BAD DATA DESIGN!
  - Do not put any UI property into the database.
- Get `name` variable from `useSelector` as well as car list.
  - ```jsx
    // "./components/CarList.js"
    // ...
    const CarList = () => {
      const dispatch = useDispatch();

      const { cars, name } = useSelector(({ form, cars: { data, searchTerm } }) => {
        const filteredCars = data.filter((car) => {
          return car.name.toLowerCase().includes(searchTerm.toLowerCase());
        })
        return {
          cars: filteredCars,
          name: form.name
        };
      });
      // ...
      const renderedCars = cars.map(car => {
        const bold = name && car.name.toLowerCase().includes(name.toLowerCase());

        return (
          <div key={car.id} className={`panel ${bold && "bold"}`}>
            // ...
          </div>
        )
      })
      // ...
    };

    export default CarList;
    ```

## Adding Styling

- `./index.js`
  - `import "bulma/css/bulma.css";`
  - `import "./styles.css";`
- `./App.js`
  - Change topmost `div` element into `<div className="container is-fluid>"`.
- `./styles.css`
  - ```css
    /* "./styles.css" */
    .car-form {
      margin-top: 15px;
      padding: 15px;
    }
    .car-form .field-group {
      display: flex;
      margin-bottom: 0px;
    }
    .car-form form {
      display: flex;
      justify-content: space-between;
    }
    .car-form .field {
      display: flex;
      align-items: center;
      margin-bottom: 0px;
      margin-right: 10px;
    }
    .car-form .field label.label {
      margin-bottom: 0px;
      width: 70px;
    }
    .car-form input {
      width: 100%;
    }
    .car-form h4.subtitle.is-3 {
      margin-bottom: 0.75rem;
    }

    .car-list .panel.bold {
      font-weight: 600;
    }

    .car-list .panel {
      padding: 10px;
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      align-items: center;
    }

    .car-list p {
      font-size: 1.5rem;
    }

    .car-list .note {
      font-style: italic;
      display: block;
      margin: 40px 20px;
    }

    .car-value {
      font-size: 2rem;
      text-align: right;
    }

    hr {
      margin: 1rem 0;
    }

    .list-header {
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      align-items: center;
    }

    .list-header h3.title {
      margin-bottom: 0px;
    }

    .list-header .search {
      display: flex;
      align-items: center;
    }
    .list-header .search .label {
      margin-bottom: 0px;
      margin-right: 10px;
    }
    ```
