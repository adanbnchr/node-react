footer: © Node Program, 2016
slidenumbers: true

# Node Program
## Express.js

![inline 70%](images/np-logo120.png)

Node.js version: 5.1
Last updated: Jan 2016

---

### Express

Express is the most popular web application framework for Node. It is easy to work with as it ties into Node's functional paradigm.

* Deliver static content (or consider using nginx)
* Modularize business logic
* Construct an API
* Connect to various data sources

---

### DEMO

Core http module API: <http://bit.ly/1StXFsG> 

:computer: :confused:

---

# With Express you can develop APIs faster!

---

### Express vs. http

* URL params and query strings parsing
* Automatic response headers
* Routes and better code organization
* Myriads of plugins (called middleware)
* Request body parsing (with a module)
* Authentication, validation, session and more! (with modules)


---

### Installing Dependency

```
$ npm install express --save
```

```
$ npm install express@4.13.3 --save
```
---


## Installing Scaffolding

Install Express.js command-line generator:

```
$ npm install -g express-generator
```

---

### Using the Generator

```
$ express todo-list-app
$ cd todo-list-app
$ npm install
$ node app
```

^Go through all aspects of the generated app and point out various features. Specifically talk about app.js and how it abstracts away some things for testing

---

### Structure

* `app.js`: main file, houses the embedded server and application logic
* `/public`: contains static files to be served by the embedded server
* `/routes`: houses custom routing for the embedded server
* `/views`:  contains templates that can be processed by a template engine

---

## app.js

1. Imports and instantiations
2. Configurations
3. Middleware
4. Routes
5. Bootup

---

## Configuring Express

The Express server needs to be configured before it can start

Manage configuration via the `set` method:

```
var express = require('express')
var app = express()
app.set('port', process.env.PORT || 3000)
app.set('views', 'templates') // The directory the templates are stored in
app.set('view engine', 'jade')
```

---

## Node.js Middleware Pattern

---

### What is Middleware

Middleware pattern is a series of processing units connected together, where the output of one unit is the input for the next one. In Node.js, this often means a series of functions in the form:

```js
function(args, next) {
  // ... Run some code
  next(output) // Error or real output
}
```

---

## Continuity

Request is coming from a client and response is sent back to the client.

```
request->middleware1->middleware2->...middlewareN->route->response
```

---

### Organizing Code

`database` in `app.js`, but we need it in `routes/users.js` where our `/users` routes are located

How to pass the `database` reference? Something like this?

```
var users = require('./routes/users.js')(database)
```

There is a better way!

---

### Connect Framework

Express leverages the Connect framework to provide the middleware functionality. Middleware are used to manage how a request should be handled.

---

### Applying Connect/Express Middleware

Example:

```js
var express = require('express')
var app = express()
//... Define middleware1-N
app.use(middleware1)
app.use(middleware2)
...
app.use(middlewareN)
...
```

---

### Middleware Order

Middleware are executed in the order specified:

```js
var logger = require('morgan')
var bodyParser = require('body-parser')
...
app.use(logger('dev'))
app.use(bodyParser.json())
```


---

### Two Categories of Express Middleware 

1. npm modules, e.g., `body-parser`
2. Custom middleware

---

### Creating Middleware

Custom middleware is easy to create with a reference:

```js
var middleware = function (request, response, next) {
  // Modify request or response
  // Execute the callback when done
  next()
}
app.use(middleware)
```

---

### Creating Middleware

Or with anonymous function definition:

```js
app.use(function (request, response, next) {
  // Modify request or response
  // Execute the callback when done
  next()
})
```

---

### Passing References

`request` is **always** the same object in the lifecycle of a single client request to the Experss server

This solves the database reference problem:

```js
app.use(function (request, response, next) {
  request.database = database
  next()
})
```

---

### Most Popular and Useful Connect/Express Middleware

`$ npm install <package_name> --save`

* [body-parser](https://github.com/expressjs/body-parser) request payload
* [compression](https://github.com/expressjs/compression) gzip
* [connect-timeout](https://github.com/expressjs/timeout) set request timeout
* [cookie-parser](https://github.com/expressjs/cookie-parser) Cookies
* [cookie-session](https://github.com/expressjs/cookie-session) Session via Cookies store

---

### Connect/Express Middleware

* [csurf](https://github.com/expressjs/csurf) CSRF
* [errorhandler](https://github.com/expressjs/errorhandler) error handler
* [express-session](https://github.com/expressjs/session) session via in-memory or other store
* [method-override](https://github.com/expressjs/method-override) HTTP method override
* [morgan](https://github.com/expressjs/morgan) server logs
* [response-time](https://github.com/expressjs/response-time)

---

### Connect/Express Middleware

* [serve-favicon](https://github.com/expressjs/serve-favicon) favicon
* [serve-index](https://github.com/expressjs/serve-index)
* [serve-static](https://github.com/expressjs/serve-static) static content
* [vhost](https://github.com/expressjs/vhost)

---

## Other Popular Middleware

* [cookies](https://github.com/jed/cookies) and [keygrip](https://github.com/jed/keygrip): analogous to `cookieParser`
* [raw-body](https://github.com/stream-utils/raw-body)
* [connect-multiparty](https://github.com/superjoe30/connect-multiparty), [connect-busboy](https://github.com/mscdex/connect-busboy)
* [qs](https://github.com/visionmedia/node-querystring): analogous to `query`
* [st](https://github.com/isaacs/st), [connect-static](https://github.com/andrewrk/connect-static) analogous to `staticCache`

---

## Other Popular Middleware

* [express-validator](https://github.com/ctavan/express-validator): validation
* [less](https://github.com/emberfeather/less.js-middleware): LESS CSS
* [passport](https://github.com/jaredhanson/passport): authentication library
* [helmet](https://github.com/evilpacket/helmet): security headers
* [connect-cors](http://github.com/antono/connect-cors): CORS
* [connect-redis](http://github.com/visionmedia/connect-redis)

---

### Template Engine

Setting the `view engine` variable to `jade` for instance, would trigger
the following function call internally

```js
app.set('view engine', 'jade') // Shorthand

// Does the same as the above
app.engine('jade', require('jade').__express)
```

---

### Template Engine

Custom callbacks can be defined to parse templates

```js
app.engine([format], function (path, options, callback) {
  // Template parsing logic goes here
});
```

Note: custom callbacks are useful if the template engine doesn't export an **__express** function

---

### Express Bootup

```js
var http = require('http'),
    express = require('express')

var app = express()

// ... Configurations, middleware and routes

var server = http.createServer(app)
server.listen(app.get('port'), function () {
  // Do something... maybe log some info?
});
```

---

### Bootup 2

```js
var http = require('http'),
    express = require('express')

var app = express()

// ... Configurations, middleware and routes

app.listen(app.get('port'), function () {
  // Do something... maybe log some info?
});
```

---

### Launching the App

```
$ node server
$ nodemon server
$ node-dev server
$ forever server
$ pm2 server
```

---

# Express is awesome! :rocket:

---


# Building a RESTful API

---

### Traditional Web App

Also called thick server.

![inline](images/traditional-web.png)

---

### Traditional Web App Problems

* Slow and single-tasked (not multitasking)
* Poor and unresponsive UX (user experience)
* Duplication of data hogs bandwidth (HTML)

---

### API + AJAX/XHR Web App

Also called thick client

![inline](images/rest-web.png)

---

### Advantages of a Thick Client

* Responsive interface and UX
* Only data is transmitted (JSON)
* Re-use of the core functionality
* Asynchronous tasks
* Real-time apps

---


### Node, SPAs and REST

Build an API once and use everywhere

![inline](images/Node_soa_rest.png)

---

### API Decomposition

![inline](images/API_decomposition.png)

---

### Microservices

![inline](images/Microservices.png)

---

### REST Basics

REpresentational State Transfer (REST) is an architectural pattern for developing network applications

REST systems aim to keep things simple when connecting to and exchanging data between machines

---

### Why HTTP?

HTTP is the ideal protocol for REST, given its stateless nature and client-server architecture

* REST is far simpler compared to Remote Procedure Calls (RPC) and Web Services (SOAP, UDDI, etc)
* RPCs and Web services rely on complex vocabularies for communication
* Each new operation is a new vocabulary entry, increasing code complexity

---

### REST Verbs

REST uses HTTP requests (and verbs) for CRUD operations

* GET
* PUT
* POST
* DELETE

---

### REST Verbs

And sometimes...

* PATCH
* HEAD
* OPTIONS

---

### Common Endpoints

```
GET    /tickets     - Retrieve a list of tickets
GET    /tickets/12  - Retrieve a specific ticket
POST   /tickets     - Create a new ticket
PUT    /tickets/12  - Update ticket #12
DELETE /tickets/12  - Delete ticket #12
PATCH   /tickets/12  - Partially update ticket #12
OPTIONS /tickets/12  - What can I do to ticket #12?
HEAD    /tickets/12  - What headers would I get if I tried to get ticket #12?
```

---

### "Resources"

Resources are entities that can be stored on a computer, such as:

* Files
* Database entries
* Processed output from functions

---

### "Resources"

REST uses HTTP requests and responses to provide **representations** of resources

For example, the current version of a file available for download via its URL is a representation of a file resource

Modifying a resource, such as changing the contents of a file or deleting it, is also a resource state that can be represented via requests and responses in a REST system

---

## REST API Examples

---

### Handlers Signatures

* `function(request, response, next) {}`: request handler signature
* `function(error, request, response, next) {}`: error handler signature

---

### GET Route

```js
app.get('/users', function (request, response) {
  // Code to retrieve users
  response.send(user)
})
```

---

### Accessing URL Parameters

A URI segment can be parameterized by prefixing it with a semi-colon

```js
app.get('/users/:id/:another/:segment', function (request, response) { ... })
```

These dynamic parameters can then be accessed via the request's **params** object

GET /users/:id

```
request.params.id
```

---

### Multiple URL Parameters

GET /users/:id/:some/:filter

```
request.params.id
request.params.some
request.params.filter
```

---

### GET

To allow retrieval by id...

```js
app.get('/users/:id', function (request, response) {
  var id = request.params.id
  // Code to retrieve a single user
  response.send(user)
})
```

---

### GET

GET handlers can also be used to retrieve a collection of resources

```js
app.get('/users', function (request, response) {
  // Code to retrieve multiple users
  response.send(users)
})
```

---

### POST

To create a resource...

```js
app.post('/users', function (request, response) {
  var username = request.body.username
  var email = request.body.email
  // ...
  // Code to create a new user
  response.send(user)
});
```

Or maybe just send back the endpoint to get the user...

```js
response.send('/api/user/' + user.id)
```

---

### PUT

To update a resource (or create if it doesn't exist, perhaps)...

```js
app.put('/users/:id', function (request, response) {
  var id = request.params.id
  // Check if the user exists
  ...
  if (exists) {
    // Code to modify the user
  } else {
    // Code to create the user
  }
  response.send(user);
});
```

---

### DELETE

To delete a resource, create a DELETE handler for the desired URI

```js
app.delete('/users/:id', function (request, response) {
  var id = request.params.id;
  // code to delete the user
  response.send(user); // or maybe the URL to create a new user?
});
```

Note: `del` is [deprecated](https://github.com/jspears/mers/issues/33).

---

### HTTP Requests

A client's HTTP request is accessible from within routing handlers

It is the first argument in the handler's callback

```js
app.get('/users/:id', function (request, response) {
  // 'req' is the enhanced http request object
});
```

Note: access to the request object grants insight into the client's HTTP request, providing data on the request header, body, et al.

---

### Query Strings

Express converts a URL's query string into JSON

It can be accessed via the request's **query** object 

```
GET http://localhost:3000/?name=Bruce+Wayne&age=40&occupation=Batman
```

```js
request.query.name // "Bruce Wayne"
request.query.age // "40"
request.query.occupation // "Batman"
```

---

### Request Body

Enable the `json()` and `urlencoded()` middleware to convert raw form data into JSON

```
$ npm install body-parser --save
```

---

### Parsing Request Body

Import middleware:

```
var bodyParser = require('body-parser')
```

Parse `application/json`

```
app.use(bodyParser.json());
```

Usage: single-page applications and other JSON REST clients.

---

### Parsing Request Body

Parse `application/x-www-form-urlencoded`

```
app.use(bodyParser.urlencoded({extended: false}))
```

Usage: web forms with `action` attribute.

^Extended false is querystring and true is qs. The "extended" syntax allows for rich objects and arrays to be encoded into the URL-encoded format, allowing for a JSON-like experience with URL-encoded

---

### Accessing Form Data

Form data is then accessible via the request's **body** object (ulrencoded)

```
// POST name=Bruce+Wayne&age=40&occupation=Your+Average+Businessman
```

```js
request.body.name
request.body.age
request.body.occupation
```

---

### File Uploads

File uploads from web forms (multipart/form-data) can be parsed with these libraries:

* <https://github.com/expressjs/multer>
* <https://github.com/yahoo/express-busboy>
* <https://github.com/mscdex/connect-busboy>
* <https://github.com/andrewrk/node-multiparty>

---

### Parsing JSON

Parse various different custom JSON types as JSON

```js
app.use(bodyParser.json({ type: 'application/*+json' }))
```

---

### Parsing Buffer

Parse some custom thing into a Buffer

```js
app.use(bodyParser.raw({ type: 'application/vnd.custom-type' }))
```

---

### Parsing HTML

Parse an HTML body into a string

```js
app.use(bodyParser.text({ type: 'text/html' })
```

---

### HTTP Verbs and Routes

* `app.get(urlPattern, requestHandler[, requestHandler2, ...])`
* `app.post(urlPattern, requestHandler[, requestHandler2, ...])`
* `app.put(urlPattern, requestHandler[, requestHandler2, ...])`
* `app.delete(urlPattern, requestHandler[, requestHandler2, ...])`
 
--- 

### HTTP Verbs and Routes

* `app.all(urlPattern,  requestHandler[, requestHandler2, ...])`
* `app.param([name,] callback)`:
* `app.use([urlPattern,] requestHandler[, requestHandler2, ...])`

---

### Request

* `request.params`: parameters middlware
* `request.param`: extract one parameter
* `request.query`: extract query string parameter
* `request.route`: return route string
 
---

### Request

* `request.cookies`: cookies, requires cookieParser
* `request.signedCookies`: signed cookies, requires `cookie-parser`
* `request.body`: payload, requires `body-parser`

---

### Request Header Shortcuts

* `request.get(headerKey)`: value for the header key
* `request.accepts(type)`: checks if the type is accepted
* `request.acceptsLanguage(language)`: checks language
* `request.acceptsCharset(charset)`: checks charset
* `request.is(type)`: checks the type
* `request.ip`: IP address

^These shortcuts a.k.a. sugarcoating

---

### Request Header Shortcuts

* `request.ips`: IP addresses (with trust-proxy on)
* `request.path`: URL path
* `request.host`: host without port number
* `request.fresh`: checks freshness
* `request.stale`: checks staleness
* `request.xhr`: true for AJAX-y requests

---

### Request Header Shortcuts

* `request.protocol`: returns HTTP protocol
* `request.secure`: checks if protocol is `https`
* `request.subdomains`: array of subdomains
* `request.originalUrl`: original URL

---

### HTTP Responses

The response object is also accessible via routing handlers in Express

It is the second argument in the handler's callback

```js
app.get('/users/:id', function (request, response) {
  // 'response' is the enhanced response from http
})
```

The response object can be used to modify an HTTP response before sending it out

---

### Express Response Method

* `response.redirect(status, url)`: redirect request
* `response.send(status, data)`: send response
* `response.json(status, data):` send JSON and force proper headers
 
---

### Express Response Method

* `response.sendfile(path, options, callback)`: send a file
* `response.render(templateName, locals, callback)`: render a template
* `response.locals`: pass data to template

---

### HTTP Status Codes

To specify a status code, use the response object's **status** function

```js
app.get('/user/:id', function (request, response) {
  // Logic to check for user
  if (!exists) {
    response.status(404)
  } else if (authorized) {
    response.status(200)
  } else {
    response.status(401)
  }
  // ...
});
```

---

### HTTP Status Codes

* 2XX: for successfully processed requests
* 3XX: for redirections or cache information
* 4XX: for client-side errors
* 5XX: for server-side errors

Note: for 3xx status codes, the client must take additional action following the completion of the current request

---


### Sending a Response

Use the response object's **send** function to send the client a response

```js
app.get('...', function (request, response) {
  response.send('Hello World!')
})
```

---

### Sending a Response

The content-type is determined given the type of argument passed

```js
response.send('Hello World!')       // Content-type: text/plain
response.send([ 5, 7, 9 ])          // Content-type: application/json
response.send({ name: 'John Doe' }) // Content-type: application/json
```

---

### Sending a Response

The content-type can also be hardcoded

```js
response.set('Content-Type', 'text/plain')
response.send('Just regular text, no html expected!')
```

---

### Sending an Empty Response

```js
response.status(404).end()
```

---

### Sessions

HTTP is a stateless protocol - information about a client is not retained over subsequent requests

Use sessions to overcome this problem

Enable the `cookieParser` and `session` middleware to process cookies

---

### Sessions

```js
app.use(express.cookiesParser())
app.use(express.session({ secret: 'notastrongsecret' }))
```

The session is now accessible via `request.session`

```js
app.get('...', function (request, response) {
  var session = request.session
})
```


---

### Redis Store with Express

```
$ npm install connect-redis express-session
```

```js
var session = require('express-session'),
  RedisStore = require('connect-redis')(session)

app.use(session({
  store: new RedisStore(options),
  secret: 'keyboard cat'
}))
```

---

### Load-balancing

* Clusters
* Nginx
* HAProxy
* Varnish

---

# DEMO

RESTful API with Express: <https://github.com/azat-co/rest-api-express> 

💻 :grin:

```
$ git clone https://github.com/azat-co/rest-api-express.git
$ cd rest-api-express
$ npm install
$ node express.js
```

^Need mongod running

---

### Alternatives


* Sails
* LoopBack :point_left:
* Meteor
* Hapi
* Restify

---

### More Alternatives

Registry of hand-picked Node frameworks: [nodeframework.com](http://nodeframework.com)

---

### Questions and Exercises

❓🙋:+1:

---

## Workshop

🔨

```
$ npm i -g expressworks
```

<https://github.com/azat-co/expressworks>

Videos for solutions: [YouTube ExpressWorks Playlist](https://www.youtube.com/watch?v=C2IqQOLCCoU&list=PLguYmmjtxbWGwQRxXqMTQCj6FNb55aFVo) 

or <http://bit.ly/1jW1sBf>
