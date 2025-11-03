# 🧭 Redux in React – Quick Cookbook

## 🍳 Ingredients
```bash
npm install @reduxjs/toolkit react-redux
```

---

## 1️⃣ Create the Store

**File:** `src/store.js`
```js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

---

## 2️⃣ Create a Slice

**File:** `src/counterSlice.js`
```js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1 },
    decrement: (state) => { state.value -= 1 },
    reset: (state) => { state.value = 0 },
  },
});

export const { increment, decrement, reset } = counterSlice.actions;
export default counterSlice.reducer;
```

---

## 3️⃣ Wrap App with Provider

**File:** `src/main.jsx` or `src/index.jsx`
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

---

## 4️⃣ Use Redux in Components

**File:** `src/App.jsx`
```jsx
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement, reset } from './counterSlice';

function App() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div style={{ textAlign: 'center', marginTop: '3rem' }}>
      <h1>Counter: {count}</h1>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>−</button>
      <button onClick={() => dispatch(reset())}>Reset</button>
    </div>
  );
}

export default App;
```

---

## ✅ You’re done!

You now have:
- A global Redux store  
- A slice with actions and reducer  
- React components connected to global state  

---

## 🧠 Pro Tips
- For **async logic (API calls)** → use `createAsyncThunk()`  
- For **organization** → group slices under `/features/`  
- Redux DevTools are **built-in** with `configureStore()`
