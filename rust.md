# Rust

## Types

| Type Category/Name         | Description                                                                  | Example                                                |
| -------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------ | ---- | ------- |
| **Scalar Types**           | Represent single values.                                                     |                                                        |
| \* Integer Types           | Whole numbers. Signed and unsigned variants with different sizes.            | `let age: i32 = 30;`                                   |
| \* Floating-Point Types    | Numbers with decimal points (`f32` and `f64`).                               | `let price: f64 = 99.99;`                              |
| \* Boolean Type (`bool`)   | Truth values: `true` or `false`.                                             | `let is_active: bool = true;`                          |
| \* Character Type (`char`) | Single Unicode scalar values.                                                | `let initial: char = 'A';`                             |
| \* Unit Type (`()`)        | Represents absence of value, empty tuple.                                    | `fn greet() -> () { println!("Hello"); }`              |
| **Compound Types**         | Group multiple values into one type.                                         |                                                        |
| \* Tuple Types             | Fixed-size, ordered list of elements of potentially different types.         | `let person: (String, i32) = ("Bob".to_string(), 40);` |
| \* Array Types             | Fixed-size, ordered list of elements of the _same_ type.                     | `let numbers: [i32; 3] = [1, 2, 3];`                   |
| **Reference Types**        | Refer to data without taking ownership (borrowing).                          |                                                        |
| \* References (`&`)        | Borrow access to data, can be immutable (`&T`) or mutable (`&mut T`).        | `let x = 5; let ref_x: &i32 = &x;`                     |
| **String Types**           | For working with text.                                                       |                                                        |
| \* `String`                | Owned, growable, UTF-8 encoded string, stored on heap.                       | `let name: String = String::from("Rust");`             |
| \* String Slices (`&str`)  | Reference to a sequence of UTF-8 bytes, often part of a `String` or literal. | `let message: &str = "Hi!";`                           |
| **Slice Types**            | Dynamically sized views into contiguous sequences.                           |                                                        |
| \* Slices (`&[T]`)         | Views into arrays, vectors, etc. `&str` is a slice of bytes.                 | `let arr = [10, 20, 30]; let sl: &[i32] = &arr[..];`   |
| **Struct Types**           | User-defined data structures, group related fields.                          | `rust<br>struct Point { x: i32, y: i32 }`              |
| **Enum Types**             | User-defined types with a fixed set of possible values (variants).           | `rust<br>enum Result { Ok, Err }`                      |
| **Function Pointer Types** | Pointers to functions.                                                       | `let fn_ptr: fn(i32, i32) -> i32 =                     | x, y | x + y;` |
| **Trait Objects**          | Dynamically sized type referring to a type implementing a trait.             | `fn paint(item: &dyn Drawable) { item.draw(); }`       |
| **Never Type (`!`)**       | Computations that never return (diverging functions).                        | `fn example() -> ! { panic!("Never returns"); }`       |
| **Raw Pointers**           | C/C++-like pointers for low-level memory manipulation (unsafe).              | `let raw_ptr: *const i32 = &5 as *const i32;`          |

## `String` vs `&str`

Internally 

Similarities:

- both are UTF-8 encoded
- both contain a reference to memory location
- neither use null-termination bytes
- both contain the string length

Differences:

| Feature           | `String`                                  | `&str` (slice)                                                                                       |
| ----------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **Ownership**     | Owns                                      | Borrows                                                                                              |
| **Memory**        | Heap-allocated (dynamic size)             | Can point to various memory locations (string literals, parts of `String`, etc.)                     |
| **Mutability**    | Mutable                                   | Immutable                                                                                            |
| **Size**          | Dynamic (resizable)                       | Fixed (determined at compile time or when slicing)                                                   |
| **Creation Cost** | More expensive (heap alloc)               | Cheaper (just pointers and length)                                                                   |
| **Use Cases**     | own, modify, or build strings dynamically | view or pass string data without ownership, e.g. function arguments, string literals, and efficiency |
| **Internals**     | Contains pointer, length and capacity     | Contains pointer and length                                                                          |
Convert between `String` and `&str`:

```rust
let foo = "hello" // start with string literal &str
let bar = foo.to_string() // convert to owned String
let car = &bar // get string literal &str of bar with borrow operator
```

## Commands

```bash
cargo watch -x run
cargo watch -x "run --bin mem"
```

## Macros

```
  println!()   // ! means macro
```

## Shadowing

```rust
  let spaces = "   "; // string literal
  let spaces = spaces.len(); // shadow string with number variable
  // spaces is now a number, shadowing the previous string variable
```

- useful for series of transformations on a value while keeping same name
- limits access to original variable
- lasts until scope ends
- can be used on immutable variables

```rust
fn main() {
    let input = "123";
    match input.parse::<i32>() {
        Ok(parsed_value) => {
            let input = parsed_value; // Shadow with parsed integer
            println!("Parsed integer: {}", input);
            // original string 'input' no longer directly accessible here
        }
        Err(_) => {
            println!("Failed to parse input.");
        }
    }
}
```
