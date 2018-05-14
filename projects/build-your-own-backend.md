---
title: Build Your Own Backend
module: 4
---

## Abstract

Practice makes practice. Last week's project had you dive head first into a full-stack application with Node, Express, Knex, and a front-end to boot. That was a lot thrown at you in one week so this time around we are going to focus solely on the backend. You'll be building your own API for a large dataset of your choosing.

The main focus of this project will be to reinforce your understanding of CRUD methods, querying a database, and responding with JSON data. You can do this project right now, without any new lessons.

The secondary focus of this project is prepping your Capstone project so you have data ready to go and can dive into building a super badass UI instead of wasting a week in Node land. It's not necessary that you find perfect data for your Capstone, but it's vital that you know how to setup production APIs that are well tested. You can easily replace the source of your data, so don't worry if you can't come up with a good data source or if you have no idea what you want to build for the Capstone. Find or create interesting data that you might want to use in the future.


## Base Expectations

### Find a Data Source

Data source MUST BE LOCKED IN by Monday EOD . We don't want you wasting the whole week trying to find data. Having production API endpoints well tested is more important.

Possible sources of data:

* Work with and scrape new data from an API or website. Some APIs and websites are easier to work with than others - you may not be able to pull off the thing you want to do - be prepared for that.
  * https://developer.nrel.gov/
  * https://sunlightfoundation.com/api/
  * https://developer.foursquare.com/
  * data.world
* Parse CSVs or JSON files (Node has built-in modules for parsing CSVs)
* Create the data yourself. You must create a ‘seed file’ with a minimum of ~30 rows of data for each main table

**Note:** You can base your BYOB project on your module 3 personal project data, but then you cannot use your module 3 personal project as your final capstone project.

### Relationships

At minimum, you must have at least 2 different tables with at least 1 relationship (e.g. one-to-one, one-to-many, many-to-many).

### Required endpoints

* 4 GET endpoints
  * 2 GET endpoints for all of one resource (i.e. '/api/v1/merchants')
  * 2 GET endpoints for a specific resource (i.e. '/api/v1/merchants/:id')
* 2 POST endpoints
* 2 PUT OR PATCH endpoints
* 2 DELETE endpoints

### Status Codes & Error Handling

All endpoints should respond with the minimum status code results:

* 200/201: Success
* 404: Not Found

If POST/PUT/PATCH request fails to save an entity due to bad information being sent from the client, you should respond with

* 422: Unprocessable Entity

If you have a critical server error, you should respond with

* 500: Internal Server Error

You are welcome to use other status codes.

In addition to responding with the appropriate status code, you are expected to send back clear, informative error messages when something goes wrong. Do not simply `console.log` 'WHATEVER'. If a `POST` request fails because the request didn't include a required parameter, respond with something like 'Entity requires a <fieldName> but none was provided.'

### Custom API Endpoints

* Developer must use query params on at least one endpoint, which would allow the user to narrow down the nature of their request or filter their results. So for example, you may have an endpoint like:

  `GET api/v1/merchants?areacode=80202`

  Which would limit the results to merchants in the 80202 area code.

  A further example of this implementation can be found here: [params](https://scotch.io/tutorials/use-expressjs-to-get-url-and-post-parameters)

* Developer must secure at least 4 endpoints with a JWT. Though this project doesn't have an explicit frontend, at the `/` root of your application, return an `index.html` file with a form that allows a user to request a JWT. The user must submit an email address and an app name. If the email address ends in `@turing.io`, the JWT should include admin privileges. Require a JWT with admin privileges for any 'write' requests (POST/PUT/PATCH/DELETE).

### Documentation

In the README, developer should provide documentation on the API endpoints that can be hit. Here is a [great example of in-depth documentation](https://developer.github.com/v3/repos/) Pay attention to the information provided and the format that it's presented in.

Some things you want to considering having in your API documentation:

* Endpoints available (e.g. GET /api/v1/students, POST api/v1/students)
* What parameters can be used in certain requests (e.g. For a POST request, what should be put in the request body?)
* Sample responses from endpoints (What does the response object look like for a request?)

Don't worry about fancy styling of your documentation. You can put your documentation in the README of your BYOB GitHub repository. That being said, improperly formatted information can make it very difficult to read even if it's all accurate. 

### Testing & Linting

* All endpoints need to be tested for happy and sad paths. You should assert that proper status codes are being returned as well as appropriate response data and error messages.
* You are expected to use a linter on this project and have 0 linting errors. Use the linting configuration from your first project (Palette Picker). Bonus points for using a git hook that prevents you from committing any unlinted code.

### Deployment

* Your application should be automatically deployed to Heroku via TravisCI
* TravisCI should be running your tests and linter

-----------------------------------------------

## Instructor Evaluation Points

The following set of points are distributed at the discretion of the instructor.

### Documentation

* **10 points** -  The README includes documentation for all available endpoints and how to use them. Instructor can easily follow the documentation for using the API.
* **5 points** -  The README documentation is out-of-date or inaccurate in some places. Instructor can not successfully use every endpoint based on following the documentation. 
* **0 points** - The README documentation is too sparse or inaccurate to be helpful.


### Feature Completion

* **60 points** - Developer has implemented all 10 endpoints, 4 are secured via JWTs and one is a custom endpoint that filters data based on query params. The database is seeded with at least two tables and one relationship.
* **40 points** - Developer has implemented all 10 endpoints but did not secure 4 of them with JWTs or have a custom endpoint based on query params. The database is seeded with at least two tables but without an appropriate relationship.
* **20 points** - Developer is missing endpoints and has not secured or customized any of the ones that have been implemented. The database is not seeded with two tables and one relationship.

### Testing & Linting & Error Handling

* **40 points** - Project has a running test suite that covers all happy and sad paths for the appropriate endpoints. Error handling is informative and helpful for the end-user. The project has a linting configuration that passes with no errors.
* **20 points** - Project has a running test suite that covers most happy and sad paths for each endpoint. Error handling has been implemented but does not cover all possible scenarios or is unhelpful for the end-user. Linter has some errors that need fixing.
* **10 points** - Project has significant lack of testing for happy and sad paths of endpoints. Error handling is non-existent or missing status codes and helpful messages. Linter is failing on multiple lines.

### JavaScript Style

* **40 points** - Application has exceptionally well-factored code with little or no duplication and all components separated out into logical components. There _zero_ instances where an instructor would recommend taking a different approach.
* **20 points** - Application is thoughtfully put together with some duplication and no major bugs. Developer can speak to choices made in the code and knows what every line of code is doing.
* **15 points** - Your application has some duplication and minor bugs. Developer can speak to most choices made in the code and knows what every line is doing.
* **10 points** - Your application has a significant amount of duplication and one or more major bugs. Developer cannot speak to most choices and does not know what every line of code is doing.

### Workflow

* **20 points** - Developer(s) make many small, atomic commits that clearly document the evolution of the application and do not contain irrelevant changesets that aren't reflected by the commit message. Commit messages are concise and consistent in syntax and tense. Developer(s) effectively use git branches and pull requests when applicable to incorporate changes into the application, and are not pushing directly to master. There are no instances where the developer(s) have committed source code that should be .gitignored. There are no instances of "dead" or commented-out code and debugger statements like console.log.
* **15 points** - Developer(s) make many small, atomic commits that document the evolution of the application but sometimes contain irrelevant changesets and inconsistent commit messages. Developer(s) use git branches and pull requests when applicable to incorporate changes into the application, and are not pushing fresh changes directly to master. Pull requests may contain little or no code review. There may be slight instances where the developer(s) have committed source code that should be .gitignored. There may be some instances of "dead" or commented-out code and debugger statements like console.log that need to be cleaned up.
* **10 points** - Developer(s) make large, inconsistent commits that contain irrelevant changesets and make it difficult to follow the evolution of the application. Developer(s) rarely use git branches and frequently incorporate changes directly into master with little or no review process. There are instances of committed source code that should be .gitignored and instances of dead code and/or debugger statements.
* **5 points**  - Developer(s) make very few commits that each cover too much responsibility and aren't indicative of how the application evolved. Branches and pull requests were not used and changesets were applied directly to master. There are many instances of committed source code that should be .gitignored and many instances of dead code and/or debugger statements.

-----------------------------------------------

#### Projects are due on Friday of week 4 at 1PM. Create one submission file for your project based on [this template](https://github.com/turingschool/front-end-submissions-public/blob/master/1711/mod-4/byob/submission-template.md) by copying it to a new file called YOURNAME-PARTNERSNAME.md in the same directory. Submit it as a PR to the front-end-submissions repo.

To get a **3** on this project, you need to score **125** points or higher  
To get a **4** on this project, you need to score **145** points or higher  

# Final Score: x / 170

-----------------------------------------------

#### Common Gotchas with Seeding Data:

* You'll probably have to do some sort of data manipulation/massaging before you try to seed. Think about how YOU want YOUR data to look before just dumping the data you found into your database. Do any manipulations you need to on your dataset before trying to seed. (e.g. you might want to rename, delete, or add columns)
* The data records you pull might already have unique IDs on them. Feel free to use these as your primary keys instead of wiping them out and creating new ones.
* You're going to be seeding a lot of data all at once. Recall the "Seeding Large Datasets" example from the [Knex Lesson Plan](http://frontend.turing.io/lessons/module-4/knex-postgres.html) and brush up on how to work with `Promise.all()`
* Remember you often have to RETURN the Promises you're using in your seed file. If you aren't getting any errors, but your data isn't being seeded, you're likely forgetting a `return` statement.
* If you're trying to transform data from a CSV file, avoid using [this library](https://www.npmjs.com/package/knex-seed-file).