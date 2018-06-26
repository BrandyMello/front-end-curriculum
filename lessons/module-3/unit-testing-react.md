---
title: Unit Testing React Components
module: 3
---

## Agenda

- Discuss Unit testing vs Integration Testing vs Acceptance Testing
- Learn about Jest and what we use it for
- Learn what Enzyme is and what we use it for
- Use Jest and Enzyme to take a snapshot of the UI
- Use Jest and Enzyme to test that a function was called on click
- Use Jest and Enzyme to unit test individual funtions

## Learning Goals

- Know how to add Enzyme to a project
- Know how to use Enzyme and Jest to take a snapshot of UI
- Know how to use Enzyme to simulate user behavior
- Know how to unit test a class method using Jest

## Vocab

- Jest
- Enzyme
- Mock
- Spy
- Snapshot
- Unit test
- Integration test
- Acceptance test

## Unit Testing React Components

**Golden Rule**: No copy and pasting. Part of the point of this exercise is to
build up some muscle memory.

When testing React apps, or really when testing any kind of application, most of
the tests your write will be *unit tests*. Unit tests make sure this small,
isolated, parts (units) of our application behave as expected. The more we can
write our code in a way that is testable at the unit level, the easier it will
be to scale and extend our applications.

---
**_Turn and talk_: We hear a lot about unit, integration, and acceptance tests.
What is the difference between the three of these. Take 5 minutes and see what
you can find out**

---

### Getting Started

If you haven't already, install the `create-react-app` command line tool as a global 
dependency. (To check for globally installed dependencies run the command 
`npm list -g --depth=0`)

```bash
npm install -g create-react-app
```

### Setting Up the Project

Once you have the command line tool installed, you can create a new project using 
the following command:

```bash
create-react-app grocery-list --use-npm
```

Next `cd` into the `grocery-list` directory and lets get to work.  

### Setting Up Linting

Linting is a powerful tool for maintaining code quality. Create React App uses ESLint 
for code linting. One thing that you'll notice as you build your application is that 
you'll see warnings in the console and the command line.

Add the following to your `package.json`:

```js
"eslintConfig": {
  "extends": "react-app"
}
```

[Source: this thread](https://github.com/AtomLinter/linter-eslint/issues/664)

### Clean Up Extra Stuff

Out of the box, `create-react-app` hooks you up with some boilerplate HTML and CSS that 
we won't be using. Let's clean up the existing files before we get started. First, you 
can delete the `logo.svg` file. Next, update the following files to match below:  

```js
App.css
```

```css
.App {
  max-width: 500px;
  margin: auto;
}
```

```js
// App.js

import React, { Component } from 'react';
import './App.css';

import Grocery from './Grocery'

class App extends Component {
  render() {
    return (
      <Grocery name='bananas' starred={false}/>
    );
  }
}

export default App;
```

### Running Tests

As we mentioned before, `create-react-app` has a built in testing framework that cannot 
be changed without ejecting from the boilerplate. Luckily, it's a pretty awesome test 
runner called `Jest`. [Read more about the Jest and React combo here](https://facebook.github.io/jest/docs/tutorial-react.html).

In order to run the tests, type `npm test`. Normally, our suite runs and then we return 
to the command line. With Create React App, `npm test` starts up a server that is 
constantly watching for changes. When you modify a file, the test suite will 
automatically rerun. Even better — by default, it will only watch files that have 
changed since the last time you made a git commit.

Try it out - run `npm test` to fire up the testing server. Currently our app has only 
one test file, `App.test.js`. Take a few seconds to look at that file.

**Stop and Read**: [This section on file naming conventions](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#filename-conventions).  

Traditionally, we have always put our tests into their own directory. That is absolutely 
still possible, but the Facebook team makes some good points for keeping test files in 
the same directory as their implementation. Whatever you decide to do in the future is 
up to you, but let's go with the facebook convention for the purposes of this tutorial.  

Jest is great for unit testing your app, but according to the [react docs on testing](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests) 
they "recommend that you use a separate tool for browser end-to-end tests if you need 
them. They are beyond the scope of Create React App." This means implementing our super 
friend `Enzyme`!  

```bash
npm install --save-dev enzyme react-addons-test-utils
```

### Big Picture

For this tutorial, we are building a app that allows us to make and edit a grocery list. 
In it, you can add groceries to a list, mark them `starred` or `purchased`, and filter 
based on `starred` or `purchased` tags.  

### Building and Testing the Grocery Component

We're going to start by test driving a single `<Grocery>` React component.

![React Component](/assets/images/lessons/unit-testing-react/grocery-component.gif)

Notice the following:

- The class is changing both when the component is "starred" as well as "purchased."
- The "Purchase" button says "Unpurchase" when the item is purchased.
- The "Star" button says "Unstar" when the item is starred.

To get started, make the following two files in the `src` folder of your project:

- `Grocery.js`
- `Grocery.test.js`

In `Grocery.js`, let's add a simple component:

```js
import React from 'react';

const Grocery = ({ name, quantity, notes, purchased, starred, onPurchase, onStar, onDelete }) => {
  return (
    <article className="Grocery">
      <h3>{name}</h3>
    </article>
  );
};

export default Grocery;
```

It's a functional, stateless component. Right now, we're not using a number of the 
properties we're passing. Don't worry, we will.

In `Grocery.test.js`, we'll start with a simple test to see if the `name` property 
is properly rendered in the component when passed in as a prop.

```js
import React from 'react';
import { shallow, mount } from 'enzyme';

import Grocery from './Grocery';

describe('Grocery', () => {

  it('renders the name of the grocery in <h3> tags', () => {
    const wrapper = shallow(<Grocery name="Bananas" />);
    const title = <h3>Bananas</h3>;

    expect(wrapper.contains(title)).toEqual(true);
  });

});
```

As previously mentioned, `create-react-app` uses [Jest](http://facebook.github.io/jest/) 
instead of Mocha. That said, you'll notice that the syntax is surprising similar. One 
difference is that Jest includes its own expectation library which is similar to Chai's 
`expect` syntax instead of an `assert` syntax.  

[Jest Assertions](https://facebook.github.io/jest/docs/api.html)  

If you run `npm test` you should see your one test pass (two if you still have the 
generic App test). You can keep this process running. The test suite will automatically 
run whenever you make a change to the test file.

#### Testing for a class

A common mistake developers make when testing components is writing assertions that are 
very closely tied to the actual content and text on the page. This is problematic because 
copy is much more likely to change on a regular basis. For example, if we write an 
assertion to test that the title of our application exists and contains the text 'Grocery 
List', and then we decide to change it to 'Shopping Cart', now we have a failing test 
when nothing is actually broken.

For this reason, (among others), it's usually more reliable to test things that are less 
likely to change, such as a class name on an element. An easy way to do that is to test if 
it matches a given css selector.

```js
// Grocery.test.js  

it('has a class of .Grocery', () => {
  const wrapper = shallow(<Grocery name="Bananas" />);

  expect(wrapper.is('.Grocery')).toEqual(true);
});
```

Now you should have three passing tests.  

### Testing for dynamic changes  

When you're testing your components, you're mainly testing presentation logic. Given our UI 
will change based on application data (our component props), we'll want to make sure we 
have tests for any conditional logic or dynamic changes. For example, our grocery component 
changes visually based on whether or not the grocery item has been starred or purchased. 

We could start out with a simple test to see if it has the appropriate class if it's starred.

```js
it('should have a className of "starred" if is starred', () => {
  const wrapper = shallow(
    <Grocery name="Bananas" starred={true} />
  );

  expect(wrapper.is('.starred')).toEqual(true);
});
```

This will fail. To fix it, we need to set up a conditional within our component that checks 
if the property `starred` has been passed in as a prop. Let's be fancy and use either a 
ternary or an `&&` condition in a template literal interpolation in `Grocery.js`.

```jsx
<article className={`Grocery ${starred ? 'starred' : ''}`}>
```

```jsx
<article className={`Grocery ${ starred && 'starred' }`}>
```

Try both of those out and verify that they get the test passing. Then let the uneasy feeling 
settle in as you consider that as time goes on, you'll have to do this repeatedly — first 
with `purchased` and then possibly with more properties as requirements change down the line.

**Stop and Read**: To make our lives easier, we'll use the [classnames](https://www.npmjs.com/package/classnames) 
package from npm. Check out the documentation before moving forward. You can install it with 
`npm install -S classnames`.

[classnames]: https://www.npmjs.com/package/classnames

We'll import it into the module, add some pre-made css, and refactor our `Grocery.js` 
component as follows:

```bash
touch src/Grocery.css
```

```css
.Grocery {
  border: 1px solid rgb(91,126,154);
  margin-top: 1em;
  margin-bottom: 1em;
  padding: 1em;
}

.Grocery h3 {
  margin-top: 0;
}

.Grocery.purchased {
  opacity: 0.5;
}

.Grocery.starred {
  background-color: rgb(91,126,154);
  color: rgb(160,182,196);
}
```

```js
import React from 'react';
import classnames from 'classnames';
import './Grocery.css';

const Grocery = ({ name, quantity, notes, purchased, starred, onPurchase, onStar, onDelete }) => {
  return (
    <article className={classnames('Grocery', { starred })}>
      <h3>{name}</h3>
    </article>
  );
};

export default Grocery;
```

#### Your Turn

When `starred` is true, it will be included as a class name. But, how do we 
know that it's not always included? Also, what about the `purchased` property?

Write the following tests and the implementation to match:

- `it('should not have a className of "starred" if it is not starred')`
- `it('should have a className of "purchased" if it is purchased')`
- `it('should not have a className of "purchased" if it is not purchased')`

### Optional Components

Sometimes Steve's son has strong opinions on how many of something he should get. 
For example, Wes wanted _two_ packs of "[circle cheeses][]" the other day. The same 
is true for how he feels about specifics. He _does not_ like the "Light" variety 
and he better end up with the cheddar-flavored derivatives.

To help with this, our grocery application allows the user to specify an optional 
quantity or notes. These aren't always needed. Let's say you know exactly what kind 
of vegan sardines you want. In these cases, you shouldn't have to clutter up the 
user interface with "Quantity: NaN" or anything along those lines.

[circle cheeses]: http://mini-babybel.com/products/

Let's start with a test:

```js
// Grocery.test.js

it('should have a p.Grocery-quantity element if a quantity is passed as a prop', () => {
  const wrapper = shallow(
    <Grocery name="Bananas" quantity={'17 bunches'} />
  );

  expect(wrapper.find('.Grocery-quantity').length).toEqual(1);
});
```

Like jQuery, Enzyme has the `find` command which will traverse the component to find 
what I'm looking for.

Depending on what projects you've worked on, you may or may not have experience 
conditionally displaying elements. So, I'll help you out with this one. There are 
multiple ways to do this, but here we'll use an `&&` conditional to create a clean 
in-line statement in our JSX.

```js
const Grocery = ({ name, quantity, notes, purchased, starred, onPurchase, onStar, onDelete }) => {
  return (
    <article className={classnames('Grocery', { starred, purchased })}>
      <h3>{name}</h3>
      { quantity && <p className="Grocery-quantity">Quantity: {quantity}</p> } // Our new code.
    </article>
  );
};
```

#### Your Turn

So, does that functionality even work? Let's write a test to make sure the element 
isn't present if there is no quantity. While we're at it, let's do the same for the 
notes as well. Write the following tests and implement the features to get them to pass.

- `it('should not have a p.Grocery-quantity element if a quantity is not passed as a prop')`
- `it('should have a p.Grocery-notes element if notes are passed as a prop')`
- `it('should not have a p.Grocery-notes element if notes are not passed as a prop')`

### Testing for the Changing Text on the Buttons

First of all, we need buttons to exist at the base of our Grocery item. As noted in the 
screenshot above, those buttons will say "Purchase" and "Star" (and "Remove" but we'll 
get to that later) on component mount. When a user clicks on these buttons they should 
change to "Un-purchase" or "Unstar".

```js
describe('.Grocery-purchase button', () => {

  it('should have a text of "Purchase" if purchased is false', () => {
    const wrapper = shallow(
      <Grocery name="Bananas" purchased={undefined} />
    );

    expect(wrapper.find('.Grocery-purchase').text()).toEqual('Purchase');
  });

  it('should have a text of "Unpurchase" if purchased is true', () => {
    const wrapper = shallow(
      <Grocery name="Bananas" purchased={true} />
    );

    expect(wrapper.find('.Grocery-purchase').text()).toEqual('Unpurchase');
  });

});
```

#### Your Turn

- Can you implement the functionality that passes the tests above?
- Can you write the tests and implementation for the "Star" button as well?
- Can you make a "Remove" button? It's probably not _super_ important but we're 
  going to need it in the next section and now seems like as good a time as any to make 
  it, right? Don't worry about the functionality for now, just make it exist.
- Can you write a test to see if the quantity field has the correct text in it?
- Can you write a test to see if the notes field has the correct text in it?

### Testing the Button Functionality

So, the buttons say the right things. That's cool, but how do we know that they do 
the things we expect them to?

First, we should start by being clear about what should happen. If you look closely 
at the code, you'll see that `onPurchase`, `onStar`, and `onRemove` properties are being 
passed in. It stands to reason that these functions should be called when one of those 
fancy buttons we just made are tested.

At this point, we don't necessarily care what the functions are doing -- just that they 
are being called appropriately. This is where **mocks** come in handy. Mocks are stubbed-in 
or "faked" functionality that allows us to unit test specific parts of our code without having 
to worry about others. A mock will override the behavior of a specific function and provide 
you with utilities to test the interaction with the mock instead.

You'll find when testing applications that use a framework like React, you'll need to make 
a significant amount of mocks. And that's ok! Sometimes it may feel like you're faking too 
much of your code by mocking so many pieces of functionality. The general rule here is if 
you're not testing the actual behavior **within** the code you are mocking, it's perfectly 
fine to mock it.

So let's use mocks to test that the functions we passed in are being called appropriately.

Consider the following test:

```js
it('should call the onPurchase prop when clicked', () => {
  const onPurchaseMock = jest.fn();

  const wrapper = mount(
    <Grocery
      name="Bananas"
      purchased={true}
      onPurchase={onPurchaseMock}
    />
  );

  wrapper.find('.Grocery-purchase').simulate('click');

  expect(onPurchaseMock).toBeCalled();
});
```

- `jest.fn()` returns a special mock function that we can use but also test to see if 
  it was called.
- `wrapper.find('.Grocery-purchase').simulate('click');` will simulate a click event.
- `expect(onPurchaseMock).toBeCalled()` asks our mock function if it was called. Ideally, 
  when we wire it up to the appropriate button, this will be true.
- We are now using the `mount` function to create our wrapper rather than the `shallow` 
  rendering we've used previously. Doing a full mount will allow us to more easily test 
  methods on our components. Read more about the differences between [shallow rendering](https://github.com/airbnb/enzyme/blob/master/docs/api/shallow.md) 
  and [full mounting](https://github.com/airbnb/enzyme/blob/master/docs/api/mount.md).

#### Your Turn

- Can you add an `onClick` function to the "Purchase" button and be the hero who makes 
  the test pass?
- We likely want to pass in a grocery ID or grocery name to the `onPurchase` method so 
  we can keep track of what has been purchased. Can you add an assertion to the previous 
  test to check that `onPurchaseMock` was called with the correct arguments? 
  ([Hint](https://facebook.github.io/jest/docs/expect.html#content))
- Can you write the tests and implementation for the "Star" and "Remove" buttons?

### Homework: Implementing the Grocery List

![](/assets/images/lessons/unit-testing-react/grocery-list-component.gif)

(**Important Note for Careful Readers**: You're not responsible for the form... just the 
list below...which means you'll probably need to provide some fake grocery data in your tests)

The list should have the following functionality (test driven, of course):

- It shows all of the groceries. Can you test to make sure that it shows the appropriate number of groceries?
- There is a "Clear Groceries" button that is disabled unless there are one or more groceries on the list.
- When the "Clear Groceries" button has been pressed the `onClearGroceries` property function should be called.
- Not shown: Can you test and implement a counter that keeps track of the number of groceries in the list?
