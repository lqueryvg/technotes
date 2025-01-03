# Typescript

## Setup

```
yarn add -D typescript
yarn exec -- tsc --init
```

## Basic types

```typescript
string;
number;
boolean;
```

## Special types & set operations

Types are like sets and contain the set of all types that a value can be.

- `|`: union
- `&`: intersection
- `never`: empty set

  ```typescript
  type X = 1 | never; // 1
  ```

- `any`: universal set

  ```typescript
  type X = 1 | any; // any
  ```

- `unknown`: we don't know the type (yet!)

## Arrays

```typescript
number[]
Array<number>   // same as above; "generic" version
[number]        // a tuple
```

## type annotations on variables

```typescript
const myName: string = "Alice"; // type not actually needed; inferred
```

## Function parameters

```typescript
function greet(name: string) {
  // param type will be checked
}
```

## Optional properties

```typescript
function printName(obj: { first: string; last?: string }) {
    ...
}
// Both OK
printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" });
```

## Function return values

```typescript
function getNum(): number {
  // return type not needed; inferred
  return 42;
}
```

## Objects

```typescript
function printCoord(pt: { x: number; y: number }) {
    // param is an object with expected properties
    ...
}

function printName(obj: { first: string; last?: string }) {
    // `last` is an optional property
    ...
```

It's an error to try to access an optional propery without first checking it is
defined, e.g. use: `obj.last?.toUpperCase()`

## Unions (`|`)

```typescript
function printId(id: number | string) {
  // id may now be number or string
  ...
```

If it's the union of two _objects_, then only common
keys can be accessed.

## Intersections (&)

```typescript
type B = A & B;
```

Combines all the properties of A and B.

## Guards - typeof

- Use typeof conditionals to safely work with a param

  ```typescript
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  }
  ```

- Use `Array.isArray(x)` to determine if a param is an array.

- `==`, `&` and `!=` check for `null` or `undefined`

### Guard - null & undefined

```typescript
    let x: number | null | undefined
    if (x != null) {
    if (x != undefined) { // same as above
        // x must now be a number
```

## Aliases

```typescript
type Animal = {
  name: string;
};

type Bear = Animal & {
  honey: boolean;
};

function printAnimal(animal: Animal) {}

type ID = number | string;
```

Aliases are not types themselves: using an alias is
exactly the same as using the original type

## Interfaces

like an alias, but can be redefined

```typescript
interface Animal {
  name: string;
}

interface Animal {
  size: number; // not possible with types
}

interface Bear extends Animal {
  honey: boolean;
}
```

## Type assertions

like a cast; use when you know more about the type than TS does

```typescript
let someValue: unknown = "this is a string"
let strLength: number = (someValue as string).length

// The following is the same, but does not work in JSX
let strLength: number = (<string>someValue).length

const x = "hello" as number;        # error
```

## Index Signatures

```typescript
interface BooleanDictionary {
  [key: string]: boolean;
}
```

##  Optional Chaining

```typescript
let x = foo?.bar.baz();
```

## Non-null assertion

```typescript
let s = e!.name; // stop complaining about e possibly being null (not an actual assert)
const foo = someFunc()!; // post-fix
```

## Nullish Coalescing

```typescript
let x = foo ?? bar; // x = bar if foo is null or undefined
```

## Maps

```typescript
const scores = new Map<string, number>([
  ["a", 1],
  ["b", 2],
]);
const total = scores.get("a");
```

## Bitwise Operators inside enums

```typescript
enum Gift {
  Coal = 0b00000,
  Train = 0b00001,
  Bicycle = 0b00010,
  SuccessorToTheNintendoSwitch = 0b00100,
  TikTokPremium = 0b01000,
  Vape = 0b10000,
  Traditional = Train | Bicycle,
  OnTheMove = Coal | Bicycle | TikTokPremium | Vape,
  OnTheCouch = (OnTheMove & ~Bicycle) | SuccessorToTheNintendoSwitch,
}
```

## Misc

```typescript
"a".charCodeAt(0); // ascii
"string".substring(i, 5);
"string".charAt(i)[("1", "2")].map(Number); // convert all strings to numbers
```
