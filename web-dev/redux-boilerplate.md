# Redux Boilerplate

Store.js

```javascript
//store.js
import { configureStore } from '@reduxjs/toolkit';
import underlyingReducer from '../features/underlyings/underlyingSlice';
// import { createLogger } from 'redux-logger';

// const logger = createLogger({
//   collapsed: true,
//   predicate: (getState, action) => action.type !== 'socket/priceDataAdded'
// });

export const store = configureStore({
  reducer: {
    underlying: underlyingReducer,
  }
  // middleware: (getDefaultMiddleware) => getDefaultMiddleware().concat(logger)
});
```

Slice.js

```javascript
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

const apiUrlBase = 'www.google.com';

export const fetchListings = createAsyncThunk('fetchDerivativeListings', async (token) => {
  let finalData = {
    payload: []
  };
  const response = await fetch(`${apiUrlBase}/derivatives/${token}`);
  let stockData = await response.json();
  return finalData;
});

const initialState = {
  isLoading: false,
  data: {
    stockData: []
  },
  isError: false
};

const derivativesSlice = createSlice({
  name: 'derivatives',
  initialState,
  reducers: {
    priceDataAdded: (state, action) => {
      state.priceData[action.payload.token] = action.payload.price;
    },
  },
  extraReducers: (builder) => {
    builder.addCase(fetchListings.pending, (state) => {
      state.isLoading = true;
      state.data.stockData = [];
    });
    builder.addCase(fetchListings.fulfilled, (state, action) => {
      state.isLoading = false;
      let { payload } = action;
      if (payload.success) {
        state.data.stockData = payload.payload;
        state.isError = false;
        state.data.message = '';
      } else {
        state.data.stockData = [];
        state.data.message = payload.err_msg;
        state.isError = true;
      }
    });
    builder.addCase(fetchListings.rejected, (state, action) => {
      console.log('Error ', action.payload);
    });
  }
});

export const { priceDataAdded } = derivativesSlice.actions;
export default derivativesSlice.reducer;
```

component.js

```
import { useDispatch, useSelector } from 'react-redux';

const state = useSelector((state) => state.underlying);
const socketData = useSelector((state) => state.socketData.priceData);
```
