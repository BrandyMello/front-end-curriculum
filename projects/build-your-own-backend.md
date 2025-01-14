---
title: Build Your Own Backend
module: 4
---

## Abstract

BYOB is a one week long solo project. It is ungraded and intended as a way to get comfortable with building databases using Express, Knex, and PostgreSQL. You will be building a RESTful API, complete with professional-grade documentation. This knowledge will be directly applicable to the next paired project, Palette Picker.

The skills you will be building in BYOB are:

- building your own RESTful API for a large dataset of your choosing
- one-to-many relational database schema design
- deploying your API to Heroku

## Base Expectations

### Find a Data Source

Data source and initial one-to-many schema design MUST BE LOCKED IN by Tuesday morning. We don't want you wasting the whole week trying to find data.

Possible sources of data:

* Work with and scrape new data from an API or website. Some APIs and websites are easier to work with than others - you may not be able to pull off the thing you want to do - be prepared for that.
  * https://developer.nrel.gov/
  * https://sunlightfoundation.com/api/
  * https://developer.foursquare.com/
  * data.world
* Parse CSVs or JSON files (Node has built-in modules for parsing CSVs)
* Create the data yourself. You must create a ‘seed file’ with a minimum of ~30 rows of data for each main table

### Relationships

At minimum, you must have at least 2 different tables with at least 1 relationship (e.g. one-to-one, one-to-many, many-to-many).

Each table must have no fewer than three columns.

### Required endpoints

* 4 GET endpoints
  * 2 GET endpoints for all of one resource (i.e. '/api/v1/merchants')
  * 2 GET endpoints for a specific resource (i.e. '/api/v1/merchants/:id')
* 2 POST endpoints
* 1 DELETE endpoint

### Status Codes & Error Handling

All endpoints should respond with the minimum status code results:

* 200/201: Success
* 404: Not Found

If POST request fails to save an entity due to bad information being sent from the client, you should respond with

* 422: Unprocessable Entity

If you have a critical server error, you should respond with

* 500: Internal Server Error

You are welcome to use other appropriate status codes.

In addition to responding with the appropriate status code, you are expected to send back clear, informative error messages when something goes wrong. Do not simply `console.log` 'WHATEVER'. If a `POST` request fails because the request didn't include a required parameter, respond with something like `'Entity requires a <fieldName> but none was provided.'`

### Documentation

In the README, developer should provide documentation on the API endpoints that can be hit. Here is a [great example of in-depth documentation](https://developer.github.com/v3/repos/) Pay attention to the information provided and the format that it's presented in.

Some things you want to considering having in your API documentation:

* Endpoints available (e.g. GET /api/v1/students, POST api/v1/students)
* What parameters can be used in certain requests (e.g. For a POST request, what should be put in the request body?)
* Sample responses from endpoints (What does the response object look like for a request?)

You can put your documentation in the README of your BYOB GitHub repository. Remember, improperly formatted information can make it very difficult to read even if it's all accurate, so be sure to utilize markdown syntax styling/formatting ([here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) is a markdown style cheatsheet).

### Deployment

* Your application should be deployed to Heroku

## Articulation Requirement

In addition to the functional requirement, on a separate dedicated git branch, go through each line of the server file and put a comment on each line that explains what that line of code is doing. Be as explicit as you can.

### Extension: Create a FE documentation in your BE repo

If all other expectations and requirements are met, you may:

* Create a front-end (in the same repository as BYOB) that will document the API.
* Provide a page that documents the available endpoints, the data that will be received, and the data the user must send.
* The site should be interactive, allowing a developer to "try out" endpoints. Think of building out something like Postman specifically for your back-end.
* This front-end documentation page should be a single page and can be written in Javascript, jQuery, or React.

-----------------------------------------------

## Check Ins

We will have one check in over the course of the project.

-----------------------------------------------

#### Common Gotchas with Seeding Data:

* Often, people think they have to do all their seeding in one step, but you can take whatever steps you need. Scraping data from the web? Consuming another API? You can do those all in separate steps! Get your data, _then_ clean it, _then_ write your seed file!
* You'll probably have to do some sort of data manipulation/massaging before you try to seed. Think about how YOU want YOUR data to look before just dumping the data you found into your database. Do any manipulations you need to on your dataset before trying to seed. (e.g. you might want to rename, delete, or add columns)
* You're going to be seeding a lot of data all at once. Recall the "Seeding Large Datasets" example from the [Knex Lesson Plan](http://frontend.turing.io/lessons/module-4/knex-postgres.html) and brush up on how to work with `Promise.all()`
* Remember you often have to RETURN the Promises you're using in your seed file. If you aren't getting any errors, but your data isn't being seeded, you're likely forgetting a `return` statement.
* If you're trying to transform data from a CSV file, avoid using [this library](https://www.npmjs.com/package/knex-seed-file).
