# Installing Redux in JavaScript

This snippet provides instructions for installing and setting up Redux in a JavaScript or React TS/JS project.

### Using npm
```bash
npm install react-redux @reduxjs/toolkit
```

### Using yarn
```bash
yarn add redux react-redux
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


## Additional Resources

- [Redux Official Documentation](https://redux.js.org/)
- [React-Redux Documentation](https://react-redux.js.org/)
- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
