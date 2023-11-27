# Typescript

Setup

    yarn add -D typescript
    yarn exec -- tsc --init

Basic types

    string
    number
    boolean

Arrays

    number[]
    Array<number>   # same as above; see generics
    [number]        # different!; see tuples

type annotations on variables

    let myName: string = "Alice";
        # type not actually needed; inferred

Function parameters

    function greet(name: string) {
        # param will be checked
    }

Function return values

    function getNum(): number {
        # return type not usually needed; inferred
    }

Objects

    function printCoord(pt: { x: number; y: number }) {
        # param type is an object with expected properties
    }

    function printName(obj: { first: string; last?: string }) {
        # optional property

        # it's an error to try to access an optional propery without
        # first checking for undefined
        # or, safely use: obj.last?.toUpperCase()
    }

Unions (|)

    function printId(id: number | string) {
        # id may be number or string

    # if it's the union of two objects, then only common
    # keys can be accessed (since we don't know which it is)

Intersections (&)

    type B = A & B   # combines all the properties of A and B

    # all keys can be accessed

Guards - typeof

    # use typeof conditionals to safely work with the param
    if (typeof id === "string") {
        console.log(id.toUpperCase())
    }

    # use Array.isArray(x) to determine if a param is an array

    # == & != check for null or undefined

Guard - null & undefined

    let x: number | null | undefined
    if (x != null) {
    if (x != undefined) { // same as above
        // x must now be a number

Aliases (not preferred; use interfaces first)

    type Animal = {
        name: string
    };

    type Bear = Animal & {
        honey: boolean
    }

    function printAnimal(animal: Animal) {
    }

    type ID = number | string;

    # Aliases are not types themselves: using an alias is
    # exactly the same as using the original type

Interfaces

    like an alias, but can be extended

    interface Animal {
        name: string
    }

    interface Animal {
        size: number  # not possible with types
    }

    interface Bear extends Animal {
        honey: boolean
    }

Type assertions

    # like a cast; use when you know more about the type than TS does

    let someValue: unknown = "this is a string"
    let strLength: number = (someValue as string).length
    let strLength: number = (<string>someValue).length
        # same, but doesn't work in JSX

    onst x = "hello" as number;        # error

Index Signatures

    interface BooleanDictionary {
      [key: string]: boolean;
    }

Generics

    function printName(obj: { first: string; last?: string }) {
    // ...
    }
    // Both OK
    printName({ first: "Bob" });
    printName({ first: "Alice", last: "Alisson" });

Optional Chaining

    let x = foo?.bar.baz();

Non-null assertion

    let s = e!.name           // stop complaining about e possibly being null (not an actual assert)
    const foo = someFunc()!   // post-fix

Nullish Coalescing

    let x = foo ?? bar    // x = bar if foo is null or undefined

Maps:

    const scores = new Map<string, number>([
      ['a', 1],
      ['b', 2],
    ])
    const total = scores.get('a')

Misc:

    'a'.charCodeAt(0)   // ascii
    'string'.substring(i, 5)
    'string'.charAt(i)
    ['1', '2'].map(Number) // convert all strings to numbers

Sets:

never - empty set
any - universal set
| - union
& - intersection
