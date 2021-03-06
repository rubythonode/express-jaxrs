# express-jaxrs

**Git: [github](https://github.com/atrifan/express-jaxrs)**

![npm](https://img.shields.io/npm/v/express-jaxrs.svg) ![license](https://img.shields.io/npm/l/express-jaxrs.svg)

An annotation support express-jaxrs middleware for Javascript

![nodei.co](https://nodei.co/npm/express-jaxrs.png?downloads=true&downloadRank=true&stars=true)

![travis-status](https://img.shields.io/travis/atrifan/express-jaxrs.svg)

![](https://david-dm.org/atrifan/express-jaxrs/status.svg)
![](https://david-dm.org/atrifan/express-jaxrs/dev-status.svg)

# Table of contents

- [Features](#features)
- [Install](#install)
- [Scripts](#scripts)
- [Dependencies](#dependencies)
- [Contributing](#contributing)
- [Author](#author)
- [Getting started](#getting-started)
- [Usage](#usage)
    - [Using @Path annotation](#using-@path-annotation)
    - [Annotating @Path at class level](#annotating-@path-at-class-level)
        - [Defining base path](#defining-base-path)
        - [Defining default base path](#defining-default-base-path)
    - [Defining path params](#defining-path-params)
    - [Defining path prefix](#defining-path-prefix)
    - [Annotating at method level](#annotating-at-method-level)
    - [Coding a handler method](#coding-a-handler-method)
- [Examples](#examples)
- [License](#license)

## Features

### v0.1.0

- Support JAX-RS annotations
    - Path
    - POST
    - GET
    - DELETE
    - PUT
- Support path params in @Path annotation
- Support prefix path in @Path annotation

## Install

`npm install --save express-jaxrs`


## Scripts

 - **npm run test** : `./test.sh`
 - **npm run readme** : `node ./node_modules/.bin/node-readme`

## Dependencies

Package | Version | Dev
--- |:---:|:---:
[annoteJS](https://www.npmjs.com/package/annoteJS) | ^0.1.5 | ✖
[path](https://www.npmjs.com/package/path) | ^0.12.7 | ✖
[util](https://www.npmjs.com/package/util) | ^0.10.3 | ✖
[mocha](https://www.npmjs.com/package/mocha) | ^4.0.1 | ✔
[expect.js](https://www.npmjs.com/package/expect.js) | ^0.3.1 | ✔
[sinon](https://www.npmjs.com/package/sinon) | ^4.1.2 | ✔
[mocha-phantomjs](https://www.npmjs.com/package/mocha-phantomjs) | ^4.1.0 | ✔
[express](https://www.npmjs.com/package/express) | ^4.16.2 | ✔
[node-readme](https://www.npmjs.com/package/node-readme) | ^0.1.9 | ✔


## Contributing

Contributions welcome; Please submit all pull requests against the master branch. If your pull request contains JavaScript patches or features, you should include relevant unit tests. Please check the [Contributing Guidelines](contributng.md) for more details. Thanks!

## Author

Trifan Alex <trifan.alex.criss@gmail.com> undefined

## Getting started

Require the module ```express-jaxrs``` instantiate it with new and than first thing set the path to the folder containing
the js files for controllers **this should be relative to your process.cwd() or your execution position** or it can be
**an absolute path**

```
var Express = require('express'),
    JaxRS = require('express-jaxrs');

var jaxRsMiddleware = new JaxRS(),
    app = new Express();

jaxRsMiddleware.setControllerRoutes('./test_utils/controller');
app.use(jaxRsMiddleware.handle)
    .listen(<port>);
```

## Usage

This is an express middleware to simplify the routing mechanisms in express.js framework. The middleware is intended
for ES5 & ES6 javascript providing a set of JAX-RS annotations to easily create controllers.

### Using @Path annotation

The @Path annotation can be used on both classes and individual methods.

Using @Path("/") is permitted but this will not resolve default / but will resume to resolving /controller_js_file so when
you have annotate functions with @Path("/") in a controller named some_controller.js than this will be resolved to
<protocol><host>/some_controller

### Annotating @Path at class level

#### Defining base path

```
@Path("/x")
function Controller() {}

@Path("/test")
@GET()
Controller.prototype.test = function(request, response) {

}
.....
```

Annotation at class level will set the base path and @Path that follow on prototype methods just concatenate to the
basePath => that the **test** method will be called when requests come at /x/test

#### Defining default base path

```
- function_controller.js

@Path("/")
function Controller() {}

@Path("/test")
@GET()
Controller.prototype.test = function(request, response) {

}
.....
```

In this case test will resolve to /function_controller/test

### Defining path params

In order to define path params we use ```@Path("/path/{id}")```. This specifies that whatever comes after path
is the id and this can be retrieved in ```request.pathParams.id```. The name that you put inside brackets become keys
in **request.pathParams** from where you can extract the values. So the brackets work as placeholders.

### Defining path prefix

In order to define path prefix we use ```@Path("/path/**")```. This specifies that when nothing else from /path/ is matched
than everything goes to the method annotated as described

### Annotating at method level

This part is not about annotating the prototype methods but individual functions for example:

```
- individual_functions.js

@Path("/time")
@GET()
function getTime(request, response) {
   ....
}

@Path("/go")
@POST()
function post(request, response) {
    .....
}

```

Verb annotations should follow @Path annotations.

### Coding a handler method

Every method should have two parameters request and response. **request.pathParams** contains information of path variables
if specified in the @Path annotation.

Methods should return either a promise or a static object.

The static object should have the following definition:

```
{
    statusCode: ...,
    headers: { ... },
    body: { ... }
}
```

Methods that return promises should resolve also to the above definition.

- Default Content-Type is "application/json"
- Default statusCode is 200
- Default body is ''

Even though a method might not return anything and just triggers some code it should always have a return although it
can be ``` return; ```

Each js file should end with a **module.exports**.

If it's a **class** inside the file **module.exports** should look like so:

```
@Path("/")
function Controller() {}

@Path("/user")
@GET()
Controller.prototype.getUser = function(request, response) {
    return {
        statusCode: 200
    }
}

module.exports = {
    Controller: Controller
}
```

As you can see from the above example the key should have the **same name as the class name**

If in the file you only have **functions** the **module.exports** should look like so:

```
@Path("/user")
@GET()
function getAllUsers(request, response) {
    return {
        statusCode: 200
    }
}

@Path("/admin")
@GET()
function getAllAdmins(request, response) {
    return {
        statusCode: 200
    }
}

module.exports = {
    getAllUsers: getAllUsers,
    getAllAdmins: getAllAdmins
}
```

As you can see from the above example the key should have the **same name as the function names**

## Examples

A list of all the major features implemented for controllers can be found in
(here)[https://github.com/atrifan/express-jaxrs/tree/master/test_utils/controller]

## License

 - **MIT** : http://opensource.org/licenses/MIT


