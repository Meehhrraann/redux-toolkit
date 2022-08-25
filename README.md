# redux-toolkit
<details><summary>install</summary>

```js
npm i @reduxjs/toolkit
```
</details>


<details><summary>index.js</summary>

```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { createStore } from 'redux'
import rootReducer from './store/reducers/rootReducer'
import { Provider } from 'react-redux'

const store = createStore(rootReducer);

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <Provider store={store} >
      <App />
    </Provider>
  </React.StrictMode>
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```
</details>

<details><summary>store/reducers/:dart:rootReducer:dart:</summary>

```js
import { configureStore } from '@reduxjs/toolkit'
import xReducer from './xReducer'
import yReducer from './yReducer'
import fetchDataReducer from './fetchDataReducer'

  const rootReducer = configureStore({
  x: xReducer,
  y: yReducer,
  fetchData: fetchDataReducer
});

export default rootReducer
```
</details>

<details><summary>store/reducers/:dart:xReducer:dart:  +  🔔extraReducer🔔 for accessing yReducer actions (ice cream for free)</summary>

```js
import { createSlice } from '@reduxjs/toolkit'

const initialState = {
  numOfCakes: 20
}

const xReducer = createSlice({
  name: 'x',
  initialState,
  reducers: {
    decreaseWithoutPayload: (state) => { state.numOfCakes-- },
    increaseWithPayload: (state, action) => { state.numOfCakes += action.payload }
  }
  extraReducers: builder => {
    builder.addCase(yDecreaseWithoutPayload, state => { state.numOfIcecreams-- })
  }
})

export default xReducer.reducer
export const { decreaseWithoutPayload, increaseWithPayload } = xReducer.actions
```
</details>


<details><summary>store/reducers/:dart:fetchDataReducer by axios:dart: with 🔔createAsyncThunk🔔(just available on extraReducers) </summary>

```js
import axios from 'axios'
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit'

const initialState = {
  loading: false,
  users: [],
  error: ''
}

// Generates pending, fulfilled and rejected action types
export const fetchDatas = createAsyncThunk('user/fetchDatas', () => {
  return axios
    .get('https://jsonplaceholder.typicode.com/users')
    .then(response => response.data)
})

const dataReducer = createSlice({
  name: 'data',
  initialState,
  extraReducers: builder => {
    builder.addCase(fetchDatas.pending, state => {
      state.loading = true
    })
    builder.addCase(fetchDatas.fulfilled, (state, action) => {
      state.loading = false
      state.users = action.payload
      state.error = ''
    })
    builder.addCase(fetchDatas.rejected, (state, action) => {
      state.loading = false
      state.users = []
      state.error = action.error.message
    })
  }
})

export default dataReducer.reducer
```
</details>

<details><summary>1. use as dispatch using 🎯useSelector🎯 and 🎯useDispatch 🎯</summary>

```js
import { useSelector, useDispatch } from 'react-redux'
import { decreaseWithoutPayload, increaseWithPayload } from './xReducer'
...
const numOfCakes = useSelector(state => state.x.numOfCakes)
const dispatch = useDispatch()
...
onClick={() => dispatch(decreaseWithoutPayload())}
onClick={() => dispatch(increaseWithPayload(5))}

const [value, setValue] = useState(0)
<input type='number' value={value} onChange={e => setValue(parseInt(e.target.value))} />
<button onClick={() => dispatch(increaseWithPayload(value))}> + </button>
```
</details>

<details><summary>2. fetching data ---> use as dispatch using 🎯useSelector🎯 and 🎯useDispatch 🎯</summary>

```js
import React, { useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { fetchDatas } from './dataReducer'
...
const data = useSelector(state => state.data)
  const dispatch = useDispatch()
  useEffect(() => {
    dispatch(fetchDatas())
  }, [])
  ....
  <p>{data.name}</p>
```
</details>
