# Typescript Type Language

- `extends`: pattern matcher
  - use with `const` to narrow types further
- `infer`: like a capture in a pattern match
- `never`: return this when there is no match

## Use `const` with literal types

```typescript
const wrap1 = <Options extends string[]>(options: Options) => ({
  wrapped: options,
});
const wrap2 = <const Options extends string[]>(options: Options) => ({
  wrapped: options,
});

const o1 = wrap1(["a", "b", "c"]); // const o1: { wrapped: string[]; }

const o2 = wrap2(["a", "b", "c"]); // const o2: { wrapped: ["a", "b", "c"]; }
```

## Add custom variables to `process.env`

```typescript
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      MOOD_LIGHTS: "true";
      BATH_TEMPERATURE: "327.59";
      STRAWBERRIES: "chocolate";
    }
  }
}

export {}; // important !
```

## Declare types for a missing module

```typescript
declare module "santas-special-list" {
  export type Status = "naughty" | "nice";
  export type Child = {
    name: string;
    status: Status;
  };
  export type List = Child[];
}
```

Then import them...

```typescript
import { Status, Child, List } from "santas-special-list";
```

## Array `Length` and `IndexOf`

```typescript
type Length<T extends any[]> = T extends { length: infer L } ? L : never;

type IndexOf_Recurse<A, C extends string> = A extends [C, ...any]
  ? []
  : A extends [any, ...infer Rest]
  ? [any, ...IndexOf_Recurse<Rest, C>]
  : never;

type IndexOf<A, C extends string> = Length<IndexOf_Recurse<A, C>>;

type X = IndexOf<["a", "b", "c"], "b">; // 1
type X = IndexOf<["a", "b", "c"], "c">; // 2
type X = IndexOf<[], "z">; // never
```

## `BuildNTuple` & `BuildSequence`

```typescript
// Build an N-tuple of any's of requested length
type BuildNTuple<L extends number, T extends any[] = []> = T extends {
  length: L;
}
  ? T
  : BuildNTuple<L, [...T, any]>;

type X = BuildNTuple<3>; // [any, any, any]

type BuildSequence<A extends any[]> = A extends [any, ...infer Rest]
  ? Length<A> | BuildSequence<Rest>
  : never;
type X = BuildSequence<[any, any, any]>; // 1 | 2 | 3
```

## `ArrayElementType`

```typescript
// get element type of an array
type ArrayElementType<ArrayType extends unknown[]> =
  ArrayType extends (infer ElementType)[] ? ElementType : never;

type T = number[];
type X = ArrayElementType<T>; // number
```

## Reverse a string or array

```typescript
// reverse a string
type Reverse<S extends string> = S extends `${infer Head}${infer Rest}`
  ? `${Reverse<Rest>}${Head}`
  : S;
type X = Reverse<"hello">; // "olleh"

// reverse an array
type Reverse<A> = A extends [infer First, ...infer Rest]
  ? [...Reverse<Rest>, First]
  : A;
type X = Reverse<[1, 2, 3, 4, 5]>; // [5, 4, 3, 2, 1]
```

## `DeepReadonly`

```typescript
type DeepReadonly<O extends object> = {
  readonly [K in keyof O]: O[K] extends Function
    ? O[K]
    : O[K] extends object
    ? DeepReadonly<O[K]>
    : O[K];
};
type T = DeepReadonly<{ a: 1; b: { c: 1 } }>;
const x: T = { a: 1, b: { c: 1 } };
x.b.c = 1; // error: Cannot assign to c because it is a read-only property.
```

## `SlashStringToUnion`

```typescript
type SlashStringToUnion<S extends string> =
  S extends `${infer Head}/${infer Rest}` ? Head | SlashStringToUnion<Rest> : S;
type T = SlashStringToUnion<"a/b/c">; // "a" | "b" | "c"
```

## BuildArrays

```typescript
type BuildArrayOfStrings<
  L extends number,
  S extends string,
  T extends string[] = []
> = T extends { length: L } ? T : BuildArrayOfStrings<L, S, [...T, S]>;

type T = BuildArrayOfStrings<3, "abc">; // ["abc", "abc", "abc"]

type BuildArrays<
  S extends string,
  Sizes extends number
> = Sizes extends infer Head extends number
  ? BuildArrayOfStrings<Head, S>
  : Sizes extends infer Head extends
      | number
      | infer RemainingSizes extends number
  ? BuildArrayOfStrings<Head, S> | BuildArrays<S, RemainingSizes>
  : never;

type T = BuildArrays<"abc", 1 | 2 | 3>;
// [["abc"], ["abc", "abc"], ["abc", "abc", "abc"]]
```

## Defaults for generics

```typescript
type ApiRequest<T, S = "GET"> = { data: T; method: S }; // S has a default
```

## Working with object keys and values

```typescript
// prepend "get" to the name of each key and capitalise first letter
type Getters<Type> = {
  [Property in keyof Type as `get${Capitalize<
    string & Property
  >}`]: () => Type[Property];
};

type prependGood<T> = {
  [Property in keyof T as `good_${string & Property}`]: T[Property];
}; // add "good_" to the beginning of all keys

type RemoveKeysWithPrefix<Prefix extends string, T> = {
  [K in keyof T as K extends `${Prefix}${string}` ? never : K]: T[K];
};
type X = RemoveBadKeys<"bad_", { good_key: 1; bad_key: 2 }>; // { good_key: 1 }
```

### `keyof` in string interpolations

```typescript
type StringKeys<T> = `key = ${keyof T}`; // Error:
// Type 'symbol' is not assignable to type
// 'string | number | bigint | boolean | null | undefined'.

type StringKeys<T> = `key = ${keyof T & string}`; // Fixed!
```

### Make properties writable

```typescript
type MakeWritable<T> = { -readonly [P in keyof T]: T[P] };
```

## Utility types

```typescript
// Make all properties optional
type Partial<T> = { [P in keyof T]?: T[P] };

// Make all properties required
type Required<T> = { [P in keyof T]-?: T[P] };

// Make all properties readonly
type Readonly<T> = { readonly [P in keyof T]: T[P] };

// pick K properties from T
type Pick<T, K extends keyof T> = { [P in K]: T[P] };

// omit K properties from T
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;

// object with keys and values of specific types
type Record<K extends keyof any, T> = { [P in K]: T };
type T = Record<string, number>; // { [x: string]: number }

// exclude U types from T
type Exclude<T, U> = T extends U ? never : T;
type T = Exclude<number | string | string[], number | string>; // string[]
type T = Exclude<number, number | string>; // never

// extract U types from T
type Extract<T, U> = T extends U ? T : never;
type T = Extract<string, string[] | string | number>; // string
type T = Extract<string, string[] | number>; // never

// Exclude null and undefined from T
type NonNullable<T> = T & {};
type T = NonNullable<number | string | null | undefined>; // number | string
type T = NonNullable<null | undefined>; // never

// get parameters of a function type in a tuple
type Parameters<T extends (...args: any) => any> = T extends (
  ...args: infer P
) => any
  ? P
  : never;

// get return type of a function
type ReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : any;
```

## String literal built-ins

- `Uppercase`: all chars
- `Lowercase`: all chars
- `Capitalize`: first char
- `Uncapitalize`: first char
