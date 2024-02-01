
# Typescript Type Language

```
extends - pattern matcher
infer   - like a capture in a pattern match
never   - return this when there is no match

type X = 1 | never  // 1

type ArrayElement<ArrayType extends unknown[]> =
  ArrayType extends (infer ElementType)[] ? ElementType : never

type T = number[]
type X = ArrayElement<T>        // number

type Exclude<T, U> = T extends U ? never : T            // exclude type from a union

type Getters<Type> = { // add get to the name of each key a capitalise first letter
  [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
}

type prependGood<T> =  {
  [Property in keyof T as `good_${string & Property}`]: T[Property]
} // add "good_" to the beginning of all keys

type RemoveKeysWithPrefix<Prefix extends string, T> = {
  [K in keyof T as K extends `${Prefix}${string}` ? never : K]: T[K]
}
type X = RemoveBadKeys<"bad_", {good_key: 1, bad_key: 2}>  // { good_key: 1 }

// reverse a string
type Reverse<S extends string> =
  S extends `${infer Head}${infer Rest}` ?
	`${Reverse<Rest>}${Head}` : S
type X = Reverse<"hello"> // "olleh"

// reverse an array
type Reverse<A> = A extends [infer First, ...infer Rest] 
  ? [...Reverse<Rest>, First] 
  : A
type X = Reverse<[1, 2, 3, 4, 5]>  // [5, 4, 3, 2, 1] 

type DeepReadonly<O extends object> = {
  readonly [K in keyof O]:
	O[K] extends Function
      ? O[K]
      : O[K] extends object
        ? DeepReadonly<O[K]>
        : O[K]
}

type Length<T extends any[]> = 
  T extends { length: infer L } ? L : never

type IndexOfInner<A, C extends string> = 
  A extends [C, ...any]
    ? []
    : A extends [any, ...infer Rest]
      ? [any, ...IndexOfInner<Rest, C>]
      : never

type IndexOf<A, C extends string> = Length<IndexOfInner<A, C>>

type X = IndexOf<["a", "b", "c"], "b"> // 1
type X = IndexOf<["a", "b", "c"], "z"> // 2
type X = IndexOf<[], "z"> // never

type BuildTuple<L extends number, T extends any[] = []> = 
  T extends { length: L }
    ? T
    : BuildTuple<L, [...T, any]>

type X = BuildTuple<3> // [any, any, any]

type Sequence<A extends any[]> = 
  A extends [any, ...infer Rest] ? Length<A> | Sequence<Rest> : never
type X = Sequence<[any, any, any]> // 1 | 2 | 3

type SlashListToUnion<S extends string> =
  S extends `${infer Head}/${infer Rest}`
    ? Head | SlashListToUnion<Rest>
    : S

type BuildArrayOfStrings<L extends number, S extends string, T extends string[] = []> =
  T extends { length: L }
    ? T
    : BuildArrayOfStrings<L, S, [...T, S]>

type BuildArrays<S extends string, Sizes extends number> = 
  Sizes extends (infer Head extends number)
    ? BuildArrayOfStrings<Head, S>
    : Sizes extends (infer Head extends number | infer RemainingSizes extends number)
      ? BuildArrayOfStrings<Head, S> | BuildArrays<S, RemainingSizes>
      : never
```
