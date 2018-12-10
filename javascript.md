# Javascript

## arrow functions

    const functionName = param => { body; };
    const functionName = (param1, param2) => { body; };      

## `...` syntax (spread & rest)

On RHS of assigment (spread *expands* the elements):

    var other = [ 1, 2, ...rows ];    # expand all elements of 'rows' array

    var dateFields = [1970, 0, 1];
    var d = new Date(...dateFields);  # call Date() with array elements as params

    # with objects:
    var obj1 = { foo: 'bar', x: 42 };
    var obj2 = { foo: 'baz', y: 13 };
    var clonedObj = { ...obj1 };          # Object { foo: "bar", x: 42 }
    var mergedObj = { ...obj1, ...obj2 }; # Object { foo: "baz", x: 42, y: 13 }

On LHS of assignment (spread *collects* the elements):

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

