---
title: Server-Side Testing
length: 120
tags: express, testing, server, node
---

## Let's Go Through Some Examples

We'll be using [mocha](https://mochajs.org/) for our test runner, [chai](http://chaijs.com/) for our assertion library, and [chai-http](https://github.com/chaijs/chai-http) for our request generator.

Clone down [this basic app](https://github.com/turingschool-examples/test-express) to get started.

Change into the new directory and run `npm install`.

To see that the server is running, run the command `node server.js` in the terminal, and head over to `localhost:3000` in your browser. You should see a page with the text "We're going to test all the routes!"

To stop the server, just enter `control + c` in the terminal.

### Setup

Take a look at the server file, `server.js`, to see what this app is doing out of the box. We're requiring Express, creating the server application that listens on port 3000, and then setting up one route for the path `localhost:3000/`.

In your projects, you're going to be testing API requests. So let's set up an API!

For this API, this server is going to have a very simplified database using `app.locals`. Add this code to the server file:

```javascript
app.locals.students = require('./students');

app.get('/api/v1/students', (request, response) => {
  response.status(200).json(app.locals.students);
});
```

The `students.js` file contains an array of student objects, and we are loading the contents of this file into a local server variable called `app.locals.students`. Imagine this `app.locals.students` is our database's students table. Here is more on [app.locals](https://expressjs.com/en/api.html#app.locals).

Now let's install our testing tools from the terminal.

```shell
npm install -D mocha chai chai-http
```

Create a directory called `test` and create a new test file called `routes.spec.js` within the `test` directory. Open the new test file.

At the top of the test file, `routes.spec.js`, add:

```javascript
const chai = require('chai');
const should = chai.should();
const chaiHttp = require('chai-http');
const server = require('../server');

chai.use(chaiHttp);

describe('Client Routes', () => {

});

describe('API Routes', () => {

});
```

Run the test suite with the command `mocha` (you'll have to have mocha installed globally to run this command in the terminal). The test output should be something like:

```shell

0 passing (3ms)

```

This makes sense because we don't have any tests yet, but now we're all setup to add tests!

**Note:** If your tests are running, but "hanging" and not exiting (you have to enter `control + c` to exit the tests), then you might have to add `--exit` to your mocha command. To run the tests, enter `mocha --exit`.

### Happy Path

The happy path is a test case we write for when we expect everything to go well. This includes a well-formed request and an appropriate response.

As a basic test, let's test the root endpoint to our app, `localhost:3000/`, or just `/`. In the `routes.spec.js` file. Use chai-http to make the request inside of the test.

The test for the route `/` becomes:

```javascript
describe('Client Routes', () => {
  it('should return the homepage with text', done => {
    chai.request(server)
    .get('/')
    .end((err, response) => {
      response.should.have.status(200);
      response.should.be.html;
      response.res.text.should.equal('We\'re going to test all the routes!');
      done();
    });
  });
});
```

Here is the breakdown of the test:

1. Start a request to the server
2. For a specific route, use the appropriate request verb
3. When you get the response from the server, test what the response contains
- We use the `done()` function to tell mocha that the test has completed (or else the test will pass prematurely or it could timeout and fail)

The tests in this lesson are written using `should`, but you can choose to use `expect` or `assert` - just be consistent. See the [chai docs](http://chaijs.com/api/) for more info on different ways to make test assertions.

### Sad Path

Let's test a route that doesn't exist in our app. All we have to do is make a request to a garbage endpoint and we then expect a standard 404 response. Here is what our tests look like:

```javascript
describe('Client Routes', () => {
  it('should return the homepage with text', done => {
    chai.request(server)
    .get('/')
    .end((err, response) => {
      response.should.have.status(200);
      response.should.be.html;
      response.res.text.should.equal('We\'re going to test all the routes!');
      done();
    });
  });

  it('should return a 404 for a route that does not exist', done => {
    chai.request(server)
    .get('/sad')
    .end((err, response) => {
      response.should.have.status(404);
      done();
    });
  });
});
```

The status code is the very least we can test for. If you have a custom 404 error page, then you can also test for the contents of the page.

When you run the tests with `mocha`, you should see something like:

```shell
Test Express is running on localhost:3000.
  Client Routes
    ✓ should return the homepage with text
    ✓ should return a 404 for a route that does not exist


  2 passing (39ms)
```

Great! We have some basic routes tested, both a happy and sad path. Let's start testing the API routes.

Right now, this is what the entire test file looks like:

```javascript
const chai = require('chai');
const should = chai.should();
const chaiHttp = require('chai-http');
const server = require('../server');

chai.use(chaiHttp);

describe('Client Routes', () => {
  it('should return the homepage with text', done => {
    chai.request(server)
    .get('/')
    .end((err, response) => {
      response.should.have.status(200);
      response.should.be.html;
      response.res.text.should.equal('We\'re going to test all the routes!');
      done();
    });
  });

  it('should return a 404 for a route that does not exist', done => {
    chai.request(server)
    .get('/sad')
    .end((err, response) => {
      response.should.have.status(404);
      done();
    });
  });
});

describe('API Routes', () => {

});
```

### Test an API Call (GET Request)

From our basic server-side tests above, you can see how we might test our API. The first test we'll write is for the `/api/v1/students` route. A GET request to this endpoint should return a collection all of the students.

Let's write the test. In the `API Routes` describe block:

```javascript
describe('API Routes', () => {
  describe('GET /api/v1/students', () => {
    it('should return all of the students', done => {
      chai.request(server)
      .get('/api/v1/students')
      .end((err, response) => {
        response.should.have.status(200);
        response.should.be.json;
        response.body.should.be.a('array');
        response.body.length.should.equal(3);
        response.body[0].should.have.property('lastname');
        response.body[0].lastname.should.equal('Turing');
        response.body[0].should.have.property('program');
        response.body[0].program.should.equal('FE');
        response.body[0].should.have.property('enrolled');
        response.body[0].enrolled.should.equal(true);
        done();
      });
    });
  });
});
```

Run the tests with `mocha`. Because we already have the route set up in our `server.js` file, you should see something like:

```shell
Test Express is running on localhost:3000.
  Client Routes
    ✓ should return the homepage with text
    ✓ should return a 404 for a route that does not exist

  API Routes
    GET /api/v1/students
      ✓ should return all of the students


  3 passing (76ms)
```

### Test a POST Request

For a post request, we need to not only send the request to the correct endpoint, but we also need to give some information in the body of the post request.

In another `describe` block, let's write the test first:

```javascript
describe('POST /api/v1/students', () => {
  it('should create a new student', done => {
    chai.request(server)
    .post('/api/v1/students') // Notice the change in the verb
    .send({                   // Here is the information sent in the body or the request
      lastname: 'Knuth',
      program: 'FE',
      enrolled: true
    })
    .end((err, response) => {
      response.should.have.status(201); // Different status here
      response.body.should.be.a('object');
      response.body.should.have.property('lastname');
      response.body.lastname.should.equal('Knuth');
      response.body.should.have.property('program');
      response.body.program.should.equal('FE');
      response.body.should.have.property('enrolled');
      response.body.enrolled.should.equal(true);
      done();
    });
  });
});
```

Let's run the tests - we get:

```shell
Test Express is running on localhost:3000.
  Client Routes
    ✓ should return the homepage with text
    ✓ should return a 404 for a route that does not exist

  API Routes
    GET /api/v1/students
      ✓ should return all of the students
    POST /api/v1/students
      ✓ should create a new student
      1) should create a new student

  4 passing (79ms)
  1 failing

  1) API Routes
       POST /api/v1/students
         should create a new student:

      Uncaught AssertionError: expected { Object (domain, _events, ...) } to have status code 201 but got 404
      + expected - actual

      -404
      +201
```

Well of course. We expect a 201 response from the post request, but we get a 404 because we haven't created the route in our `server.js` file. Let's do that. In the `server.js` file, add:

```javascript
app.post('/api/v1/students', (request, response) => {
  app.locals.students.push(request.body); // No validation here...
  response.status(201).json(app.locals.students[app.locals.students.length - 1]);
});
```

Now we get this _abysmal_ error:

```shell
1 failing

  1) API Routes POST /api/v1/students should create a new student:
     Uncaught AssertionError: expected '' to be an object
      at chai.request.post.send.end (test/routes.spec.js:62:33)
      at Test.Request.callback (node_modules/superagent/lib/node/index.js:619:12)
      at node_modules/superagent/lib/node/index.js:795:18
      at IncomingMessage.<anonymous> (node_modules/superagent/lib/node/parsers/json.js:16:7)
      at endReadableNT (_stream_readable.js:974:12)
      at _combinedTickCallback (internal/process/next_tick.js:74:11)
      at process._tickCallback (internal/process/next_tick.js:98:9)
```

Seems like everything should work...right? However, if we `console.log()` the `request.body` in the route, we get `undefined`. Turns out Express needs help parsing the body of a request. There is a package called, you guessed it, `body-parser` that can help us with this. It's already in the `package.json` file so you don't need to install it - just bring it in to the server file.

Add this line to the `server.js` file:

```javascript
const bodyParser = require('body-parser');
app.use(bodyParser.json());
```

We're using `.json()` because we expect the content in the body to be JSON. Run the tests again, and everything should pass.

### POST Sad Path

What if we make a POST request and don't specify all of the properties of a student? For instance, in the request body if we specify `{lastname: 'Knuth', program: 'FE'}`, but we leave out the `enrolled` property and value, the new record should not be created. We don't want unintended null values in our database!

We need to design our server so that it does not accept this kind of situation with missing data. Let's write the test!

```javascript
it('should not create a record with missing data', done => {
  chai.request(server)
  .post('/api/v1/students')
  .send({
    lastname: 'Knuth',
    program: 'FE' // Missing the enrolled property and value
  })
  .end((err, response) => {
    response.should.have.status(422);
    response.body.error.should.equal('You are missing data!');
    done();
  });
});
```

We need to add some validation in our POST route to see if the request body object contains all the information we need. Let's modify the route:

```javascript
app.post('/api/v1/students', (request, response) => {
  let result = ['lastname', 'program', 'enrolled'].every((prop) => {
    return request.body.hasOwnProperty(prop);
  });

  if (result) {
    app.locals.students.push(request.body);
    response.status(201).json(app.locals.students[app.locals.students.length - 1]);
  } else {
    response.status(422).send({
      error: 'You are missing data!'
    })
  }
});
```

This isn't a perfect validation because we're not testing for null values even when all the properties are specified in the post request body.

When you're working with a database, your database ORM or database engine will most likely have some built-in validation for you, but you will still have to handle the response.

Run the test suite again, and all of the tests should be passing.

### What Could Go Wrong? More Sad Paths

There are many more possibilities for route sad paths. Some could be:

* The resource requested at the endpoint does not exist (`/api/v1/students/5`, but student #5 does not exist in the database)
* For a POST request, the request does not contain all of the necessary data in the body (missing properties or null data)
* For a PUT request, the request body has missing properties or null data
* For a PUT request, a user tries to change a property of a record that does not exist in the database
* A user tries to change the primary key of a record
* A user submits duplicate data for table columns that must have unique record values
* And others!

### before and beforeEach

Server-side tests should run in isolation and each test should not leave artifacts in the database. For instance, the first test in the test file should not influence what happens with the fifth test. Therefore, we need to run migrations before we run the test suite and reset the database before each test.

If you're using a "real" database like postgreSQL with knex, you will typically need to:

 1. Before all tests, run the migrations for your test environment and seed the test database
 2. Before each test:
  * Clean out the database (delete records in all tables)
  * Seed your database with records

For this lesson, we're not using a real database, so we can just reset `app.locals` to the original data from the `students.js` file.

With our testing structure, we have built-in methods called `before` and `beforeEach`, and they run before all tests and before each test in the describe block they are scoped in, respectively. There is also `after` and `afterEach`, but there is a caveat with `afterEach`. If a test fails, the `afterEach` will _not_ run after that test, which can leave your database in a bad state. So be sure to put your database in a good state for every test even if one fails.

Let's write these methods within the `describe('API Routes', ...` block.

```javascript
before(done => {
  // Run migrations and seeds for test database
  done();
});

beforeEach((done) => {
  // Would normally run run your seed(s), which includes clearing all records
  // from each of the tables
  server.locals.students = students;
  done();
});
```

And at the top of the `routes.spec.js` file:

```javascript
const students = require('../students');
```

### File summary

By the end of it all, this is what the `routes.spec.js` file looks like:

```javascript
const chai = require('chai');
const should = chai.should();
const chaiHttp = require('chai-http');
const server = require('../server');
const students = require('../students');

chai.use(chaiHttp);

describe('Client Routes', () => {
  it('should return the homepage with text', done => {
    chai.request(server)
    .get('/')
    .end((err, response) => {
      response.should.have.status(200);
      response.should.be.html;
      response.res.text.should.equal('We\'re going to test all the routes!');
      done();
    });
  });

  it('should return a 404 for a route that does not exist', done => {
    chai.request(server)
    .get('/sad')
    .end((err, response) => {
      response.should.have.status(404);
      done();
    });
  });
});

describe('API Routes', () => {
  before(done => {
    // Run migrations and seeds for test database
    done();
  });

  beforeEach((done) => {
    // Would normally run run your seed(s), which includes clearing all records
    // from each of the tables
    server.locals.students = students;
    done();
  });

  describe('GET /api/v1/students', () => {
    it('should return all of the students', done => {
      chai.request(server)
      .get('/api/v1/students')
      .end((err, response) => {
        response.should.have.status(200);
        response.should.be.json;
        response.body.should.be.a('array');
        response.body.length.should.equal(3);
        response.body[0].should.have.property('lastname');
        response.body[0].lastname.should.equal('Turing');
        response.body[0].should.have.property('program');
        response.body[0].program.should.equal('FE');
        response.body[0].should.have.property('enrolled');
        response.body[0].enrolled.should.equal(true);
        done();
      });
    });
  });

  describe('POST /api/v1/students', () => {
    it('should create a new student', done => {
      chai.request(server)
      .post('/api/v1/students') // Notice the change in the verb
      .send({                   // Here is the information sent in the body or the request
        lastname: 'Knuth',
        program: 'FE',
        enrolled: true
      })
      .end((err, response) => {
        response.should.have.status(201); // Different status here
        response.body.should.be.a('object');
        response.body.should.have.property('lastname');
        response.body.lastname.should.equal('Knuth');
        response.body.should.have.property('program');
        response.body.program.should.equal('FE');
        response.body.should.have.property('enrolled');
        response.body.enrolled.should.equal(true);
        done();
      });
    });

    it('should not create a record with missing data', done => {
      chai.request(server)
      .post('/api/v1/students')
      .send({
        lastname: 'Knuth',
        program: 'FE' // Missing the enrolled property and value
      })
      .end((err, response) => {
        response.should.have.status(422);
        response.body.error.should.equal('You are missing data!');
        done();
      });
    });
  });
});
```

Your `server.js` file should look something like this:

```javascript
const express = require('express');
const app = express();

const bodyParser = require('body-parser');
app.use(bodyParser.json());

app.set('port', process.env.PORT || 3000);
app.locals.title = 'Test Express';
app.locals.students = require('./students');

app.get('/', (request, response) => {
  response.send('We\'re going to test all the routes!');
});

app.get('/api/v1/students', (request, response) => {
  response.status(200).json(app.locals.students);
});

app.post('/api/v1/students', (request, response) => {
  let result = ['lastname', 'program', 'enrolled'].every((prop) => {
    return request.body.hasOwnProperty(prop);
  });

  if (result) {
    app.locals.students.push(request.body);
    response.status(201).json(app.locals.students[app.locals.students.length - 1]);
  } else {
    response.status(422).send({
      error: 'You are missing data!'
    })
  }
});

app.listen(app.get('port'), () => {
  console.log(`${app.locals.title} is running on ${app.get('port')}.`);
});

module.exports = app;
```

## Checks for Understanding

* What libraries do we use to test server-side endpoints?
* What is the difference between happy and sad path tests?
* What about a response should we test?

## On Your Own - In True TDD Style

Add tests for these requests, and then implement the routes:

* GET request for one student based on their name (happy path)
  - Normally, this will be an ID, but here we'll use the name of the student. The route would look something like `/api/v1/students/knuth`
* GET request for one student based on their name that does not exist in the database (sad path)
* PUT request to change a student's information (happy path)
* DELETE request to _destroy_ a student (happy path)
