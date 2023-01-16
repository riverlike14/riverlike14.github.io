---
title: "[React] Managing Multiple Slices with Redux Toolkit"
---

# Library
- `@reduxjs/toolkit`
- `react-redux`
- `bulma`

# Adding Component Boilerplate

![App Overview](https://i.imgur.com/NcxjMmu.png)

# Thinking About Derived State

## Redux Store Design
1. Identify what state exists in the app.
2. Identify how that state changes over time.
3. Group together common pieces of state.
4. Create a slice for each group.

What State Do We Need?
- name(string)
- cost(number)
- searchTerm(string)
- cars(Array of { id, name, cost })
- totalCost(number)
- matchedCars(???)

Derived State
- Values that we can calculate using existing state.
  - totalCost
  - matchedCars
- Therefore, we don't need `totalCost` and `matchCars`.
  - We only need `name`, `cost`, `searchTerm` and `cars`.

# Thinking About Redux Design

How do the states change over time?
- change name
- change cost
- change search term
- add car
- remove car

![Redux State Design](https://i.imgur.com/0mf7ai6.png)

# Adding the Form Slice

- Form Slice
  - name, cost, changeName, changeCost
- Cars Slice
  - searchTerm, cars, changeTerm, addCar, deleteCar

```jsx
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

# Maintaining a Collection with a Slice

A slice cannot directly read another slice's state.
- `nanoid`: redux toolkit gives randomly generated id.

```jsx
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
        id: nanoid() // Math.random() works as well
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

# Creating the Store

```jsx
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

```jsx
// "./index.js"
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from "react-redux";
import { store } from "./store";
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
```

Redux side until now... Now we are going to work on the React side.

# Form Values to Update State

![Redux Changing State Design](https://i.imgur.com/nCWFUUT.png)

![Redux Accessing State Desigin](https://i.imgur.com/TJlSApq.png)

```jsx
// "./components/CarForm.js"
import { useDispatch, useSelector } from "react-redux";
import { changeName } from "../store";

const CarForm = () => {
  const dispatch = useDispatch();
  const name = useSelector(state => {
    return state.form.name;
  });

  const handleNameChange = (event) => {
    dispatch(changeName(event.target.value));
  };

  return (
    <div className="car-form panel">
      <h4 className="subtitle is-3">Add Car</h4>
      <form>
        <div className="field-group">
          <div className="field">
            <label className="label">Name</label>
            <input
              className="input is-expanded"
              value={name}
              onChange={handleNameChange}
            />
          </div>
        </div>
      </form>
    </div>
  );
};

export default CarForm;
```

# Receiving the Cost

- Import `changeCost`
- Edit line 7~12: Destructure state.form into name and cost
- Add line 18~21: New `handleCostChange` function
- Add line 36~44: Cost input field

```jsx
// "./components/CarForm.js"
import { useDispatch, useSelector } from "react-redux";
import { changeName, changeCost } from "../store";

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
  }

  return (
    <div className="car-form panel">
      <h4 className="subtitle is-3">Add Car</h4>
      <form>
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
      </form>
    </div>
  );
};

export default CarForm;
```

# Dispatching During the Form Submission

- Import `addCar`
- Add line 23~26: dispatch `addCar`
- Edit line 31, 51~53: Create Submit button

```jsx
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

![Tentative Results](https://imgur.com/JariskZ.png)

# Awkward Double Keys

```jsx
// "./components/CarList.js"
import { useSelector } from "react-redux";

const CarList = () => {
  const cars = useSelector(state => {
    return state.cars.cars
  });

  return (
    <div>CarList</div>
  );
};

export default CarList;
```

- line 6, variable `state.cars.cars` name is awkward...

![State Object in the Store](https://i.imgur.com/1MWMj0v.png)

- Change `cars` into `data`, which is more general name.

```js
// "./store/slice/carsSlice.js"
import { createSlice, nanoid } from "@reduxjs/toolkit";

const carsSlice = createSlice({
  name: "cars",
  initialState: {
    searchTerm: "",
    data: []
  },
  reducers: {
    changeSearchTerm(state, action) {
      state.searchTerm = action.payload;
    },
    addCar(state, action) {
      state.data.push({
        id: nanoid(),
        name: action.payload.name,
        cost: action.payload.cost
      })
    },
    removeCar(state, action) {
      const updated = state.data.filter(car => {
        return car.id !== action.payload;
      })
      state.data = updated;
    }
  }
});

export const { changeSearchTerm, addCar, removeCar } = carsSlice.actions;
export const carsReducer = carsSlice.reducer;
```

# Listing the Records

```jsx
// "./components/CarList.js"
import { useSelector } from "react-redux";

const CarList = () => {
  const cars = useSelector(state => {
    return state.cars.data;
  });

  const renderedCars = cars.map(car => {
    return (
      <div key={car.id} className="panel">
        <p>
          {car.name} - ${car.cost}
        </p>
        <button className="button is-danger">
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

# Deleting Records

- import useDispatch
- import action creator removeCar
- add `handleCarDelete` function

```jsx
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

![Tentative Result 2](https://imgur.com/rK6o4Aa.png)

# Adding Styling

- New CSS file
- Add this css in "./index.js" file

```css
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

- "./index.js"
  - `import "bulma/css/bulma.css";`
  - `import "./styles.css";`
- "./App.js"
  - topmost div: `<div className="container is-fluid>"`

# Form Reset on Submission

When we submit the new car, the name and the cost still remain.
- Want to reset the values whenever user submitted the form.
- Calling dispatch multiple times in a single event handler is a sign that we can improve this.

![Refactor Actions in Reducers](https://i.imgur.com/YQQDmzh.png)

Extra Reducers technique
- Tells Form combined reducer that it also needs to watch "cars/addCar" action type.
- Whenever it sees this type, reset name and cost to "" and 0.

# Reminder on ExtraReducers

- import addCar
- Add line 18~23: extraReducers technique

```js
import { createSlice } from "@reduxjs/toolkit";
import { addCar } from "./carsSlice";

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
  },
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

```jsx
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

# Derived State in useSelector

![Derived state filteredCars](https://i.imgur.com/PmTdEy9.png)

```jsx
// "./components/CarList.js"
// ..

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

# Total Car Cost

- reduce: javascript way to sum up
  - 0: initial value of acc
  - `let cost = 0; for (let car of filteredCars) { cost += car.cost }`

```jsx
// "./components/CarValue.js"
import { useSelector } from "react-redux";

const CarValue = () => {
  const totalCost = useSelector(({ cars: { data, searchTerm } }) => {
    const filteredCars = data.filter((car) => 
      car.name.toLowerCase().includes(searchTerm.toLowerCase())
    );
    return filteredCars.reduce((acc, car) => acc + car.cost, 0);
  })

  return (
    <div className="car-value">
      Total Cost: ${totalCost}
    </div>
  );
};

export default CarValue;
```

# Highlighting Existing Cars

![Bad data model](https://i.imgur.com/OiUcZ3W.png)

- Get `name` variable from `useSelector` as well as car list.
 /

```jsx
// "./components/CarList.js"
import { useSelector, useDispatch } from "react-redux";
import { removeCar } from "../store";

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

  const handleCarDelete = (car) => {
    dispatch(removeCar(car.id));
  }

  const renderedCars = cars.map(car => {
    const bold = name && car.name.toLowerCase().includes(name.toLowerCase());

    return (
      <div key={car.id} className={`panel ${bold && "bold"}`}>
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
