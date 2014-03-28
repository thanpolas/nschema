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
* **scopes** `Object` Define a set of scopes, [see Scopes](#scopes).

#### Scopes

Scopes determine the visibility and write-ability of an attribute. You may define multiple scopes representing business logic based on your particular scenario. A common set of scopes for a table of users would be: 

```js
scopes: {
    self: true, // shorthand for { get: true, set: true }
    registered: { get: true } // defaults always to true so this implies { set: true }
    anonymous: false // shorthand for { get: false, set: false }
```

The scope definition comprises of `get` and `set` boolean properties. The `get` property applies to all reading methods, while the `set` applies to all write methods.

##### Readability of attributes

We may want to propagate an Attribute's value to a view or we may not. For example, we wouldn't want to show a user's *password*. So we set the `get` scope of the *password* attribute to `false`. That way we signal the consuming library that this is an attribute that should not be propagated.

##### Write-ability of attributes

Likewise, an attribute may be the outcome of a pre-save hook, a calculated field. For these cases you do not want the consuming library to set any value. The *password* field is again an easy example as we would want to hash it before we store it.

```js
password: {
    type: 'string',
    scopes: {
        self: {get: false, set: false},
        registered: false, // equates to above
        anonymous: false,
},
```

The Scopes API was designed by [@jrpereira](https://github.com/jrpereira) while [discussing for an implementation of scopes on a Sequelize issue](https://github.com/sequelize/sequelize/issues/1462#issuecomment-38448500).



## Release History

- **v0.0.1**, *TBD*
    - Big Bang

## License

Copyright (c) 2014 Thanasis Polychronakis. Licensed under the MIT license.
