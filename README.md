# nSchema

> Node Schema Abstraction Layer

[![Build Status](https://secure.travis-ci.org/thanpolas/nschema.png?branch=master)](http://travis-ci.org/thanpolas/nschema)

## Install

Install the module using NPM:

```
npm install nschema --save
```

## Overview

Node Schema provides a normalized Schema API over popular ORM and database drivers. It can be used both to define or to read a schema with any underlying persistent storage service.

### Supported Persistent Storage Services

* [Sequelize][] For SQL databases like Postgres, MySQL, SQLlite, MariaDB, etc.
* [Mongoose][] For Mongo DB.

## API

A Node Schema a plain Object with key/value pairs. *Key* represents the attribute's name and the *Value* the type and properties of the attribute.

```js
var schema = {
    name: 'string',
    password: {
        type: 'string',
        required: true,
    },
};
```

### nSchema Types

* `string` The string type.
* `integer` The integer type.
* `float` The float type.
* `boolean` The boolean type.
* `array` The Array type.
* `object` The Object type.
* `*` Any type.

### nSchema Properties

When the value is an Object, the following properties are available:

* **type** `string` **Required** The nSchema Type.
* **required** `boolean` Default: `false` Set to true to make the attribute required.
* **allowNull** `boolean` Default: `false` Set to true to allow null values.
* **validators** `Object` Define any number of validators, built-in or custom.
* **

## Release History

- **v0.0.1**, *TBD*
    - Big Bang

## License

Copyright (c) 2014 Thanasis Polychronakis. Licensed under the MIT license.
