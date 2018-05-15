---
title: Unit Testing Redux
tags: React, Redux, Testing
module: 3
---

### Unit Testing

Unit tests, as always, aim to modularly test individual pieces of code as thoroughly as possible. In React apps, these targeted chunks of code include classes, functions, components, helper files...etc. Things that you refactor and split up. Unit testing makes your life as a developer easier and makes refactoring possible.  

After giving your function the expected arguments, do you get back what you expected?

An interesting comment I read in [this blog post](https://www.codementor.io/reactjs/tutorial/redux-unit-test-mocha-mocking) referred to unit testing as "tests written for developers", whereas integration (or acceptance) tests are written for users.  

### Testing in Redux  

In order to test Redux, there are a few things you need to mentally organize. Tests in Redux include Action tests, Reducer tests, Component tests, and any other middleware or additional libraries you may have included.  

Let's look at an example of a unit test in English.  

"I want to make sure that my todos reducer returns an array with an incremented length value in my state after receiving the ADD_TODO event."  

This differs from an integration test which alternatively would make sure that "As a user, when I'm on the home page, and I type in a Todo, and I click "Add Todo" then I see my new Todo on the page".  

Let's start with Action tests.

### Action Creators

Action creators in Redux are functions that simply return a plain object. When testing action creators we want to test whether the correct action creator was called and also whether the right action was returned.  

Take for example our `addTodo()` action.  

```js
export const addTodo = (text) => {
  return {
    type: 'ADD_TODO',
    text
  }
}
```

Given a string as text, (let's say "Find A Pumpkin"), we expect it to return an object with a key of `'ADD_TODO'` and the text "Find A Pumpkin".  

### Setup Testing with Jest  

As you learned with yesterdays Testing React lesson, Jest is a great testing framework for both unit tests AND it makes popping on Enzyme for integration tests a breeeeze! It's like Christmas!  

`npm i -D jest babel-jest`  

Make sure you have the following in your `.babelrc` file:  

```js
  {
    "presets": ["es2015"]
  }
```

Then make a quick change to the test part of `scripts` in your `package.json`.  

```js
{
  "scripts":{
    ...
    "test": "jest",
    "test:watch": "npm test -- --watch"
    ...
  }
}
```

Next, let's set up our test.  

### Action Test
`test/actions/todos.test.js`  

Step one, let's just make sure everything is wired up.

```js
import * as actions from '../../src/actions'

describe('actions', () => {
  it('should create an action to add a todo', () => {
    expect(true).toEqual(true)
  })
})

```
Run `npm test` to run your suite once, or `npm run test:watch` to test on any file changes.  

Assuming you have a passing test, let's add some substance.  

```js
import * as actions from '../../src/actions'

describe('actions', () => {
  it('should create an action to add a todo', () => {
    const text = "Go to the Vault"
    const id = 1
    const expectedAction = {
      type: 'ADD_TODO',
      text: "Go to the Vault",
      id: 1
    }
    expect(actions.addTodo(text, id)).toEqual(expectedAction)
  })
})
```

#### YOUR TURN!

Write two more unit tests to verify the functions in `actions/index.js`. file.

### Reducer Tests

Recall that Reducers connect actions to state, returning an updated state based on a given action.

In other words, a reducer receives an action and decides how to change the state based on the behavior defined in the action. We want to test that behavior.

Take a look at our todos reducer.

```js
// reducers/todos.js

const todos = (state=[], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, {id: action.id, text: action.text, completed: false}]
    case 'TOGGLE_TODO':
      return state.map(todo => {
        if (todo.id !== action.id) {
          return todo
        }
        return Object.assign({}, todo, {completed: !todo.completed})
      })
    default:
      return state
  }
}

export default todos
```

Make the new directory:  
`test/reducers/todos.test.js`  

To test this reducer, let's start by testing what happens if we don't pass this reducer any action. We want to get the default state back, which for us is an empty array.

```js
import reducer from '../../src/reducers/todos'

describe('todos reducer', () => {
  it('should return the initial state', () => {
    expect( reducer(undefined, {}) ).toEqual([])
  })
})

```

#### YOUR TURN!
Test the todos reducer for what we expect to see in State if we...

* hit the "ADD_TODO" case

```js
it('should return state with a new todo', () => {
})
```

* hit the "TOGGLE_TODO" case

```js
it('should toggle the completed status of a new todo', () => {
})
```

### Component Tests
As we've seen, it's important to separate our React components into "Presentational" vs "Container".

Presentational component testing generally involves minor mocking and stubbing, and can be thrown into the "Testing React" bucket which you are all familiar with.

Container components, however, are hooked up to Redux and use the `connect` method. Connect allows components to receive props directly from the redux `store`, rather than having to pass them through parent components.  

These props are accessed using the method `mapStateToProps`. Connect is a beast to try to test, and even the [Official Redux Testing Documentation](http://redux.js.org/docs/recipes/WritingTests.html) suggests somewhat ignoring connect and testing the component itself.  

But, because we're unique snowflakes, let's give it a go. This will be more of a conversation about various approaches to mocking data in your test files. The tests themselves could use some significant improvement.  

First, if you don't have it already, make sure Enzyme and it's test-utils buddy are installed.  
`npm i -D enzyme react-addons-test-utils`   

The container component is connected to the Redux store, which is passed down to components through the  `<Provider>` parent component. In order to test our containers we need to include the `<Provider>`, the target presentational component, and fake out a store that Provider can access.

One way to fake the redux store is to hand write a helper file and import that "store" into your test file.

```js
// test/containers/fakeStore.js

export const fakeStore = (state) => {
  return {
    default: () => {},
    subscribe: () => {},
    dispatch: () => {},
    getState: () => {
      return { ...state }
    }
  };
}
```

You might get a [syntax error about that spread operator](https://babeljs.io/docs/plugins/transform-object-rest-spread/) in your fake store file. If that happens, do the following:  
* run `npm install --save-dev babel-plugin-transform-object-rest-spread`  
* in `.babelrc` add `  "plugins": ["transform-object-rest-spread"]
`

Then in your test file, you'd reference your fake store like this:  

```js
import { fakeStore } from './fakeStore'
```
Option two is to run `npm i -D redux-mock-store`. Then at the top of your test file, include the following lines:

```js
import configureMockStore from 'redux-mock-store'
const fakeStore = configureMockStore()({todos: []})
```

Next, let's set up the information our test will need in a `setup()` function so we only have to do that part once.

```js
// test/containers/AddTodoFormContainer.test.js

import React from 'react'
import { mount  } from 'enzyme'

import AddTodoFormContainer from '../../src/containers/AddTodoFormContainer'
import AddTodoForm from '../../src/components/AddTodoForm'
import { Provider } from 'react-redux'

// get your fake store data like this if you created a helper file
import { fakeStore } from './fakeStore'

// or like this if you're using the redux module
import configureMockStore from 'redux-mock-store'
const fakeStore = configureMockStore()({todos: []})


const setup = () => {
  const props = {
    handleSubmit: jest.fn(),
  }

  const wrapper = mount(
    // if you were to test your Provider or Container components you'd need the following
    // <Provider store={fakeStore}>
    //   <AddTodoFormContainer {...props}/>
    // </Provider>

    <AddTodoForm handleSubmit={props.handleSubmit} todos={props.todos} />

  )

  const Component = wrapper.find(AddTodoForm)

  return {
    props,
    Component
  }
}
```

Then in that same file, for now we can test that the component triggers our mocked out function when we submit our form.

```js
//... setup() code goes here

describe('components', () => {
  describe('AddTodoForm', () => {

  it('should render something', () => {
      const { Component } = setup()

      expect(Component.find('form').length).toEqual(1)

      expect(Component.length).toEqual(1)
    })

    it('should call addTodo when Add Todo button is clicked', () => {
      const { props, Component } = setup()

      let form = Component.find('form')

      form.simulate('submit')
      console.log(props);
      expect(props.handleSubmit).toBeCalled()

    // Or to verify how many times a function has been called
      expect(props.handleSubmit.mock.calls.length).toBe(1)
    })
  })
})

```

### Middleware Testing  

For async action creators that hit a third party middleware (such as [redux thunk](https://github.com/gaearon/redux-thunk)), you'll need to completely Mock out the redux store (hence the difficulty). There are tools for this like [redux-mock-store](https://github.com/arnaudbenard/redux-mock-store), or [nock](https://github.com/pgte/nock) to replicate HTTP requests.  


### Resources
[Testing Section of Official Redux Docs](http://redux.js.org/docs/recipes/WritingTests.html)  
[Comprehensive Blog Post about Unit Testing Redux](https://www.codementor.io/reactjs/tutorial/redux-unit-test-mocha-mocking)  
[Blog Post About Testing Containers](http://www.wsbrunson.com/react/redux/test/2016/05/08/testing-redux-containers.html)  
