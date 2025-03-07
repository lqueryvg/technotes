# Javascript

## The problems with var

1. It is not scope safe, so you can accidentally overwrite variables in the
   outer scope:

Example 1:

    var a = "a"
    if (true) { var a = 2 }
    console.log(a)  // 2

Example 2:

    var i = 13
    for (var i=1; i<4; i++) { }
    console.log(i)  // 13

2. can use variables before they've been declared (hoisting #1):

   console.log(a) // prints "undefined", instead of an exception
   var a = 2

3. can use variables declared from an inner scope (hoisting #2:

   { var a = "2" }
   console.log(a) // 2

4. var variables are added to window object

## Debugging tip

To print variables on console.

Instead of:

    > console.log(`a = ${a}`)
    a = 1

Wrap the variable in an object:

    > console.log({a})
    { a: 1 }

## arrow functions

    const functionName = param => { body; };
    const functionName = (param1, param2) => { body; };

## `...` syntax (spread & rest)

On RHS of assigment (spread _expands_ the elements):

    const other = [ 1, 2, ...rows ];    # expand all elements of 'rows' array

    const dateFields = [1970, 0, 1];
    const d = new Date(...dateFields);  # call Date() with array elements as params

    # with objects:
    const obj1 = { foo: 'bar', x: 42 };
    const obj2 = { foo: 'baz', y: 13 };
    const clonedObj = { ...obj1 };          # Object { foo: "bar", x: 42 }
    const mergedObj = { ...obj1, ...obj2 }; # Object { foo: "baz", x: 42, y: 13 }

On LHS of assignment (spread _collects_ the elements):

    const [a, ...data] = rows;      # collector: put first (of 'rows' array) in 'a' and put rest in new 'data' array
    const [, ...data] = rows;       # collector: as above but skip first

In function declaration (rest):

    function sum( first, ...others ) { body; }  # collects rest of params in array 'others'

## Async code

- Use AWS SDK .promise()

        const AWS = require('aws-sdk')
        const Lambda = new AWS.Lambda()
        await Lambda.invoke(req).promise()

- Use promisify()

        const fs = require('fs')
        const { promisify } = require('util')
        const readFile = promisify(fs.readFile)

- Avoid await inside forEach()

- No need for callbacks in AWS event handler, e.g.

        module.exports.handler = async (event, context) => {
          const response = {
            statusCode: 200,
            body: JSON.stringify({ message: 'hello world' })
          }

          return response
        }

## Misc

    eslint --print-config file.js
    npm pack          # build local tar file
    npm install {tarball}

## Node search path

    module.paths      # contains search path

### Require with globally installed modules

By default `require()` doesn't search in global modules install location.
To `require()` globally installed modules, either use `npm link` or `NODE_PATH`.
E.g. `export NODE_PATH=/usr/local/lib/node_modules`

### Hash

    import crypto from 'crypto'
    const data = crypto.createHash('sha256').update(s).digest()

### Match

    const match = line.match(/(...)=(\S+) -> (.*)/)
    const [_, prefix, name, targetsText] = match!
