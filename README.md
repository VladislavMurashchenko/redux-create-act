# redux-create-act
[![Build Status](https://travis-ci.org/wwayne/react-tooltip.svg?branch=master)](https://www.npmjs.com/package/redux-create-act)

Small and simple util to avoid string constants for actions in redux, which doesn't force you to use FSA or custom createReducer.

## Installation
```
npm i redux-create-act
```

## Usage
Just create your action creator as usual, 
but wrap it to `createAct` like here: 
```js
createAct('YOUR_TYPE', yourActionCreator)
```
Notice that you don't need `type` field in your action.

```js
import createAct from 'redux-create-act';

const add = createAct('ADD', (payload) => ({
  payload,
}));

const subtract = createAct('SUBTRACT', (payload) => ({
  payload,
}));

const reducer = (state = 0, action = {}) => {
  switch (action.type) {
    case add.type:
      return state + action.payload;
    case subtract.type:
      return state - action.payload;
    default:
      return state;
  }
};
```

### Example with redux-thunk

```js
import createAct from 'redux-create-act';

function fetchSecretSauce() {
  return fetch('https://www.google.com/search?q=secret+sauce');
}

const makeASandwich = createAct('MAKE_SANDWICH', (forPerson, secretSauce) => ({
  forPerson,
  secretSauce,
}));

const apologize = createAct(
  'APOLOGIZE',
  (fromPerson, toPerson, error) => ({
    fromPerson,
    toPerson,
    error,
  }),
);

function makeASandwichWithSecretSauce(forPerson) {
  return function(dispatch) {
    return fetchSecretSauce().then(
      (sauce) => dispatch(makeASandwich(forPerson, sauce)),
      (error) => dispatch(apologize('The Sandwich Shop', forPerson, error)),
    );
  };
}
```
### Example with redux-saga
```js
import createAct from 'redux-create-act';
import { call, put, takeEvery } from 'redux-saga/effects';
import Api from '...';

const userFetchRequest = createAct('USER_FETCH_REQUEST', (userId) => ({
  payload: {
    userId,
  },
}));

const userFetchSuccess = createAct('USER_FETCH_SUCCESS', (user) => ({
  user,
}));

const userFetchFailed = createAct('USER_FETCH_FAILED', (message) => ({
  message,
}));

function* onFetchUser(action) {
   try {
      const user = yield call(Api.fetchUser, action.payload.userId);
      yield put(userFetchSuccess(user));
   } catch (e) {
      yield put(userFetchFailed(e.message));
   }
}

function* watchFetchUser() {
  yield takeEvery(userFetchRequest.type, onFetchUser);
}
```
