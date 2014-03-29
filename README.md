# nSchema

> Node Schema Abstraction Layer.

[![Build Status](https://secure.travis-ci.org/thanpolas/nschema.png?branch=master)](http://travis-ci.org/thanpolas/nschema)

## STILL WORK IN PROGRESS

Nothing exists yet, hang on.

## Install

Install the module using NPM:

```
npm install nschema --save
```
## <a name='TOC'>Table of Contents</a>

1. [Overview](#overview)
1. [API](#api)
    1. [nSchema Types](#nschema-types)
    1. [nSchema Properties](#nschema-properties)
    1. [Scopes](#scopes)
    1. [Validators](#validators)
1. [Relations](#relations)

## Overview

Node Schema provides a normalized Schema API over popular ORM and database drivers. It can be used both to define or to read a schema with any underlying persistent storage service.

### Supported Persistent Storage Services

* [Sequelize][] For SQL databases like Postgres, MySQL, SQLlite, MariaDB, etc.
* [Mongoose][] For Mongo DB.

[Mongoose]: http://mongoosejs.com/
[Sequelize]: http://sequelizejs.com/

## API

A Node Schema is a plain Object with key/value pairs. *Key* represents the attribute's name and the *Value* the type and properties of the attribute.

```js
var schema = {
    name: 'string',
    password: {
        type: 'string',
        required: true,
    },
};
```

**[[⬆]](#TOC)**

### nSchema Types

* `string` The string type.
* `integer` The integer type.
* `float` The float type.
* `boolean` The boolean type.
* `array` The Array type.
* `object` The Object type.
* `*` Any type.

**[[⬆]](#TOC)**

### nSchema Properties

When the value is an Object, the following properties are available:

* **type** `string` **Required** The nSchema Type.
* **required** `boolean` Default: `false` Set to true to make the attribute required.
* **allowNull** `boolean` Default: `false` Set to true to allow null values.
* **validators** `Object` Define any number of validators, built-in or custom. [See Validators](#validators)
* **scopes** `Object` Define a set of scopes, [see Scopes](#scopes).

**[[⬆]](#TOC)**

#### Scopes

Scopes determine the visibility and write-ability of an attribute. You may define multiple scopes representing business logic based on your particular scenario. A common set of scopes for a table of users would be: 

```js
scopes: {
    self: true, // shorthand for { get: true, set: true }
    registered: { get: true } // defaults always to true so this implies { set: true }
    anonymous: false // shorthand for { get: false, set: false }
}
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

**[[⬆]](#TOC)**

### Validators

The following built-in validators are available:

* **min** `number` Set the minimum length if a string or value if number.
* **max** `number` Set the maximum length if a string or value if number.

```js
schema = {
    name: {
        type: 'string',
        allowNull: false,
        validators: {
            min: 2,
            max: 30,
        }
    }
}
```

**[[⬆]](#TOC)**

#### Custom Validators

You can define a custom validator by simply providing a function as the validator's value, to fail the validation use Javascript's `throw` keyword.

```js
validators: {
    custom: function(value) {
        if (!value) {
            throw new Error('Not good');
        }
    }
};
```

**[[⬆]](#TOC)**

#### Asynchronous Custom Validators

There are two ways you can define an asynchronous custom validator, either by defining the second argument and use it as a callback or return a Promises/A+ compliant promise.

```js
validators: {
    custom: function(value, cb) {
        doSomethingAsync(value, function(outcome) {
            if (!outcome) {
                cb(new Error('Not good'));
            } else {
                cb();
            }

        }
    },
    usingPromise: function(value) {
        return doSomethindAsync(value);
    }
}
```
> When using a callback, if you set as invoking argument any value that's truthy it will fail the validator and stop the write operation.

**[[⬆]](#TOC)**

## Relations

Here's how to define the three type of relationships:

```js
var author = nschema({name: string});

var book = nschema({
    name: 'string',
    writer: author, // one-to-one relationship
    isbn: {
        type: 'string',
        required: true,
    },
});

var publisher = nschema({
    name: 'string',
    books: [book], // one-to-many relationship
});

nschema.many(author, publisher); // many-to-many relationship
```

These relationships are not required, so you can create an author without having a publisher defined. If you require that a relation item be defined you can use the expanded format of the attribute:

```js
var author = nschema({name: string});

var book = nschema({
    name: 'string',
    writer: {
        type: author, // one-to-one relationship
        required: true,
    },
    isbn: {
        type: 'string',
        required: true,
    },
});

var publisher = nschema({
    name: 'string',
    books: {
        type: [book], // one-to-many relationship
        required: true,
    }
});

// many-to-many relationship with options
nschema.many(author, publisher, {
    required: true,
});
```

## Release History

- **v0.0.1**, *TBD*
    - Big Bang

## License

Copyright (c) 2014 Thanasis Polychronakis. Licensed under the MIT license.

Original work was done in the [Entity][] repository. Loosely inspired by [mschema][].

[mschema]: https://github.com/mschema/mschema/
[entity]: https://github.com/thanpolas/entity/
