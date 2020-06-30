# Addis

Addis is a super easy and fast node express library that let's you create a professional API with sql databases.

## Installation

    $ npm install addis

## Features

- Super easy server configuration, by default basic server configurations are included.
- Quick and easy connection to database.
- Router configuration is simple and flexible
- Different accessories are present to ensure wide range of functionalities, like: - Making a route protected is just adding an object property - Token generation with key specified - Validating request bodies with JOI validator is made easy - Encrypting any data that is going to be passed to the database

## Development

Here we'll show you everything the package does.

### Server configuration

Under route setting an object containing the route name as a key and express router as a value, this libarary handles the route creation too, which will be shown below.

```sh
const addis = require('addis');
//take a look at route configuration section below to understand how the routes files are created
const route1 = require('./route1');
const route2 = require('./route2');

addis.connection('localhost', 'userName', "password", 'databaseName', (err, con) => {
    addis.server({
        port: 3000,
        token_key: "honkytonky", //if you need token generation in your app
        connection: con, // callback result of abel.connection
        app_level_middleware: [], //add app level middlewares in the array
        route_setting: { "/route1": route1, "/route2": route2 } // add routes
    });
})
```

### Route configuration

For example let's there be a table called user, with entries userName, phone, email and Password. A general configuration looks like this:

```sh
const addis = require('addis');
const express = require('express')
const router = express()
var routingEngine = addis.routing(router)

var user = null;

//All routes here
user = routingEngine.routing({
    router_type: 'get',
    router_path: '/',
    selectors: {
        table_name: 'user'
    },
    accesseries: {
        protected: false
    }
})

module.exports = user;
```

## CRUD Operations

### READ

Here are most common read operations in great detail

#### Get data

```sh
routingEngine.routing({
    router_type: 'get',
    router_path: '/',
    selectors: {
        selector: ['*'] // by default it is *, but you can add array of columns to be fetched like [userName, phone]. Then it will only fetch these
        table_name: 'user' // add table name
    },
    accesseries: {
        protected: false //determine if the route is protected or not
    }
})
```

#### Get data by id

```sh
routingEngine.routing({
    router_type: 'get',
    router_path: '/:id_column', //The name of param passed must be as same as the column name on the database.
    selectors: {
        table_name: 'user'
    },
})
```

#### Get data by "query expression"

```sh
// the equivalent statement would be SELECT * FROM table_name WHERE phone='phone' AND password='password'
routingEngine.routing({
    router_type: 'get',
    router_path: '/expression/:phone/:password', // the param name should be same as the corresponding table column name
    selectors: {
        table_name: 'student',
        expression: ['AND'] // array of query statement binders between the params
    }
})
```

### CREATE

This is the create operation on a database

```sh
routingEngine.routing({
    router_type: 'post',
    router_path: '/',
    selectors: {
        table_name: 'user',
    },
    accesseries: {
        validator: validator,  // add Joi validator object here
        protected: false,
        encryption: {       // here is the encryption section
            round: 1,
            encrypt_column: 'userName'  // specify which key in request.body to encrypt
        }
    }
})
```

### Append

Same as CREATE operation but pass id as a params in the route path, but as mentioned above the name of the param must be as same as the id column name.

```sh
...
    route_path: '/:user_id'
...
```

### Delete

```sh
routingEngine.routing({
    router_type: 'delete',
    router_path: '/:user_id',  //again the name of param should be identical to the id column name.
    selectors: {
        table_name: 'student'
    }
})
```

## Validation

This library uses [joi](https://www.npmjs.com/package/@hapi/joi) object for validation.

```sh
accesseries: {
    ...
    validator: Joi_validation_object
    ...
}
```

## Token generation

To generate token, the first part of the path must be "login".

```sh
...
router_path: '/login/:userName/:password',
...
```

## Encryption

Addis uses [bcryptjs](https://www.npmjs.com/package/bcrypt) for encryption.

```sh
accesseries: {
        ...
        encryption: {
            round: 1,        // rounds of encryption
            encrypt_column: 'password'  // Column to be encrypted
        }
        ...
    }
```

## Protected Route

If protected is true, addis checks token set in header based on the token key set on server configuration.

```sh
accesseries: {
        ...
        protected: true,
        ...
    }
```

## Coming Soon ...

Soon we'll be adding image and video support.

## License

MIT
