# Installing Redux in JavaScript

This snippet provides instructions for installing and setting up Redux in a JavaScript project.

## Installation

### Using npm
```bash
npm install redux react-redux
```

### Using yarn
```bash
yarn add redux react-redux
```

## Redux Toolkit (Recommended)

**Note:** Redux Toolkit is the recommended approach for writing Redux logic. The legacy `createStore` API is deprecated. See the legacy setup section below if needed for older projects.

### Installation

```bash
npm install @reduxjs/toolkit react-redux
```

### Basic Setup

Create a file `store.js`:

```javascript
import { configureStore, createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { count: 0 },
  reducers: {
    increment: (state) => { state.count += 1; },
    decrement: (state) => { state.count -= 1; }
  }
});

export const { increment, decrement } = counterSlice.actions;

const store = configureStore({
  reducer: counterSlice.reducer
});

export default store;
```

### Provide Store to React App

In your `index.js` or `App.js`:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

### Connect Components

```javascript
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './store';

function Counter() {
  const count = useSelector((state) => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}

export default Counter;
```

## Legacy Setup (Not Recommended)

**⚠️ Warning:** The `createStore` API is deprecated. Use Redux Toolkit's `configureStore` instead.

### 1. Create a Redux Store

Create a file `store.js`:

```javascript
import { createStore } from 'redux';

// Initial state
const initialState = {
  count: 0
};

// Reducer
function rootReducer(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'DECREMENT':
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

// Create store
const store = createStore(rootReducer);

export default store;
```

### 2. Provide Store to React App (Legacy)

In your `index.js` or `App.js`:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

### 3. Connect Components (Legacy)

Using the legacy `connect` HOC:

```javascript
import React from 'react';
import { connect } from 'react-redux';

function Counter({ count, increment, decrement }) {
  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  );
}

const mapStateToProps = (state) => ({
  count: state.count
});

const mapDispatchToProps = (dispatch) => ({
  increment: () => dispatch({ type: 'INCREMENT' }),
  decrement: () => dispatch({ type: 'DECREMENT' })
});

export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```

## Additional Resources

- [Redux Official Documentation](https://redux.js.org/)
- [React-Redux Documentation](https://react-redux.js.org/)
- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
