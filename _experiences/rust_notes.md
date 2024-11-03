---
layout: post
header-style: text
title: Rust notes
img: img/experience/Rustacean.png
---

# The Rust Book

**Rust** is a modern systems programming language focusing on safety, speed, and concurrency. It accomplishes these goals by being memory safe without using garbage collection.
To compile a rust program, in terminal just type 
```shell
rustc programfile.rs
```
This will generate the executable.

But with increasingly complex programs in low level languages handling multiple files and packages becomes hectic. Luckily, rust comes with build system and a package manager, **cargo**
To create a project using cargo
```shell
cargo new project_name
```
This created a project directory with
- a src directory which contains code files.
- Cargo.toml file : this is a configuration file for the created project, this also contains the dependencies list
- gitignore : cargo by default creates a git repo for our project, this can be customized

To build a cargo project
cd into the project directory
```shell
cargo build
```
This creates a new file **cargo.lock**  : This file specifies our exact dependencies
The executable file is in the target/debug dir
To run the program just
```shell
cargo run
```
This will check the program without actually producting the executable, this is lot faster than running an exe
```shell
cargo check
```
## Comments
- Regular comments which are ignored by the compiler:
```rust
// Line comments which go to the end of the line.
/* Block comments which go to the closing delimiter. */
```
- Doc comments which are parsed into HTML library documentation:
```rust
/// Generate library docs for the following item.
//! Generate library docs for the enclosing item.
```
## Print statements
Printing is handled by a series of macros defined in std::fmt some of which are:
- format!: write formatted text to String
- print!: same as format! but the text is printed to the console (io::stdout).
- println!: same as print! but a newline is appended.
- eprint!: same as print! but the text is printed to the standard error (io::stderr).
- eprintln!: same as eprint! but a newline is appended.

## Capturing an input

a variable is dclared with the **let** keyword:
In rust variables are immutable by default to make them mutable we use the mut keyword before the variable name.

To take the user input we use a library std.io which we include with help of **use** keyword.
for capturing an input we use the function **io::stdin()**
**read_line** this takes the users input and appends it to the specified buffer also include reference if it  is mutable.
This method can either return ok or error to capture the error we are usign **expect** function.

We have taken our input as a string to parse it into an int we first use **trim** which removes any white spaces in the string,then the **parse** function with an exception handler
**expect** 
```rust
use std::io;

let mut guess = String::new(); //the new keyword is a static/associative function for the string type 
io::stdin()
    .read_line(&mut guess)
    .expect("Failed to read line");
    let guess: u32 = guess.trim().parse().expect("Enter a number");

```
## Adding a dependency
In the cargo.toml file
```toml
[dependencies]
rand = "0.5.5"
```
after this cargo build

## Basic Rust programming concepts
### Variables
Variables are immutable by default in Rust. Therefor to edit a variable value you have to make it mutable with **mut** keyword.
```rust
    let mut x: i32 = 5;
    println!("value of x is = {}",x);
    let mut x = 6;
    println!("value of x is = {}",x);
```

**Constant values**, values that never change can be assigned using **const** keyword. constant variable names are generally declared in all upper case.
```rust
    const KILLER_COUNT: u32 = 100_0000; // underscores don't change varible values thy can be used for more easy read ability
```
Difference between variables and constants

| Feature           | Variable                      | Constant                     |
|-------------------|-------------------------------|------------------------------|
| Declaration       | `let`                         | `const`                      |
| Mutability        | Can be mutable (`mut`)        | Always immutable             |
| Type              | Inferred or explicitly stated | Must always be explicit      |
| Scope             | Local (function/block scope)  | Global (if declared globally)|
| Value Assignment  | Can be set at runtime         | Must be known at compile time|
| Naming Convention | snake_case                    | ALL_CAPS                     |

## Shadowing
In Rust refers to the ability to declare a new variable with the same name as a previous variable within the same scope. This new variable shadows the previous variable, effectively allowing the re-use of variable names with different values or types. Unlike variable mutation, shadowing allows you to change the type or value of a variable without making it making it mutable.
```rust
fn main() {
    let x = 5; // Initial binding of `x` as an integer
    let x = x + 1; // Shadowing `x`, still immutable but new value
    let x = "Shadowed as a string"; // Shadowing with a different type (string)
}
```

## Data types
### Scalar dataypes
Scalar types represent a single value and include integers, floating-point numbers, booleans, and characters.
- **Integers**
    - signed : i8, i16, i32, i64, i128, isize
    - unsigned : u8, u16, u32, u64, u128, usize
- **Floating-point numbers**
    f32, f64:(default)
- **Boolean**
    true or false
- **Character**
    represent any valid character from UTF-8
### Compound datatypes
Compound types can group multiple values into one type. 
- **Tuple** is a fixed-size collection of values of different types. Each value in a tuple can have a different type.
    ```rust
    let tuple: (i32, f64, char) = (42, 3.14, 'x');
    let (x, y, z) = tuple; // Destructuring
    ```
    You can also access tuple elements by position using a dot (.) and index:
    ```rust
    let first_value = tuple.0;
    let second_value = tuple.1;
    ```
- **Array** is a collection of values of the same type. Arrays have a fixed length that is known at compile time. 
    The array length is part of its type.
    ```rust
    let numbers: [i32; 5] = [1, 2, 3, 4, 5]; // Array of 5 integers
    let zeros = [0; 5]; // Array of five 0s
    ```
    Arrays are accessed using index notation:
    ```rust
    let first = numbers[0]; // Access first element
    ```

## Functions
In Rust functions can be declared using the **fn** keyword. The **function parameters** must have an explicit type, as Rust doesn’t allow implicit typing for function parameters. For multiple parameters each parameter must have its own type declaration.
The return type is specified after the -> symbol. Rust distinguishes between expressions and statements:
- Expressions: Produce a value and don’t end with a semicolon. For example, a + b is an expression.
- Statements: Perform some action but don’t return a value. Assignments (let x = 5;) are statements.

The return value is implicitly the last expression in the function body, without a semicolon. If you add a semicolon to the last expression, it becomes a statement, and the function will return (). You can also use the **return keyword** to return early from a function or make the return explicit. If a function doesn’t return a value, its return type is (), also known as the "unit type." You don't need to explicitly specify this unless required.
```rust
fn add(a: i32, b: i32) -> i32 {
    return a + b;  // Explicitly returning the value
}
fn sub(a: i32, b: i32) -> i32 {
    let diff = a - b;
    sub  // Returning the result without a semicolon
}
fn multiply(x: i32, y: i32) -> i32 {
    x * y
}
fn main() {
    let result = multiply(4, 5);
    println!("The product is: {}", result);
}
```

## Control flows
### if-else
- **if-else**
    ```rust
    fn main() {
        let number = 7;

        if number < 5 {
            println!("The number is less than 5");
        } else if number == 7 {
            println!("The number is exactly 7");
        } else {
            println!("The number is greater than 5 but not 7");
        }
    }
    ```
    **using if as an expression**
    ```rust
    fn main() {
        let condition = true;
        let number = if condition { 5 } else { 10 };
        println!("The number is: {}", number);
    }
    ```
- **if-let**
    if let is a simpler way to match on an Option, Result, or other pattern types when you're only interested in one specific pattern. 
    ```rust
    fn main() {
        let some_option = Some(5);

        if let Some(value) = some_option {
            println!("The value is: {}", value);
        } else {
            println!("No value found");
        }
    }
    ```
### Loop
- **Basic loop** A loop in Rust will run indefinitely unless stopped manually using a break statement. It's an infinite loop. You can return values from a loop by using the break statement.
    ```rust
    fn main() {
        let mut counter = 0;
        let result = loop {
        counter += 1;
        if counter == 10 {
            //break;
            break counter * 2;
        }
        };
    println!("Result is: {}", result);  // Prints "Result is: 20"
    }

    ```
- **while loop** The while loop continues running as long as a condition is true. T
    ```rust
    fn main() {
    let mut number = 3;
    while number != 0 {
        println!("{}!", number);
        number -= 1;
    }
    println!("Liftoff!");
    }
    ```
- **for loop** The for loop is used to iterate over a collection of items, such as arrays or ranges. 
    ```rust
    fn main() {
    for number in 1..4 {  // Range is 1 to 3 (end is exclusive)
        println!("{}!", number);
    }

    println!("Liftoff!");
    }
    ```
    Iterting over an array with for loop
    ```rust
    fn main() {
    let array = [10, 20, 30, 40, 50];

    for element in array.iter() {
        println!("The value is: {}", element);
    }
    }
    ```
- **while let** while let is similar to if let but works for loops, allowing you to run the loop as long as a pattern matches.
    ```rust
    fn main() {
    let mut stack = vec![1, 2, 3];

    while let Some(top) = stack.pop() {
        println!("The value is: {}", top);
    }
    }
    ```

### Match
The match statement is a powerful control flow construct in Rust, similar to a switch statement in other languages but much more powerful. It allows pattern matching against different values or conditions. match can be used as an expression to return values.
```rust
fn main() {
    let number = 7;

    let result = match number {
        1 => "one",
        2 => "two",
        3 => "three",
        _ => "something else",
    };

    println!("Result: {}", result);
}
```
**match with multiple patterns**
```rust
fn main() {
    let number = 7;

    match number {
        1 | 2 | 3 => println!("The number is 1, 2, or 3"),
        4..=6 => println!("The number is between 4 and 6"),
        _ => println!("The number is something else"),
    }
}

```
## Ownership in rust
Ownership model is the way to manage memory in rust.

| Memory Management    | Pros                                                   | Cons                                                  |
|----------------------|--------------------------------------------------------|-------------------------------------------------------|
| **Garbage Collector**| - Automatic memory management                          | - Non-deterministic deallocation                      |
|                      | - Reduces programmer burden                            | - Can introduce performance overhead (GC pauses)      |
|                      | - No memory leaks if managed properly                  | - Can hide memory inefficiencies                      |
| **Manual Management**| - Complete control over memory allocation/deallocation | - Prone to memory leaks and errors                    |
|                      | - Can optimize memory usage                            | - Increased programmer responsibility                 |
|                      | - Deterministic memory freeing                         | - Can lead to crashes (e.g., use-after-free)          |
| **Ownership Mode**   | - Ensures memory safety at compile-time                | - Steeper learning curve (e.g., borrow checker)       |
| (e.g., Rust)         | - No runtime overhead                                  | - Can limit flexibility with complex data structures  |
|                      | - Prevents data races in concurrent programming        | - Requires careful design to avoid ownership conflicts|

### 3 important ownership rules
1. Each value in Rust has a single owner.
A variable that holds data is the owner of that data, meaning only one variable at a time can own a piece of memory.

2. When the owner goes out of scope, the value is automatically deallocated.
Rust automatically deallocates memory when the owner of the data goes out of scope, ensuring that there are no memory leaks.

3. Ownership can be transferred (moved) to another variable.
If you assign one variable to another, the ownership of the data is moved. The original owner can no longer access the data, preventing issues like use-after-free.

In Rust, when you assign or pass a value to a function, ownership is transferred (moved) to the new variable or function unless it's a type that implements the Copy trait (like integers, booleans, and certain basic types for e.g string does not implements a copy trait).
After a move, the original variable can no longer be used.
- Passing a value to a function transfers ownership.
- Returning a value from a function gives ownership of that value to the caller.
- If you need to pass a value but still use it afterward, you should borrow the value instead of transferring ownership.

### Borrowing
Instead of transferring ownership, Rust allows you to borrow data. There are two types of borrowing:
- Immutable Borrowing (&T): You can borrow a reference to data without taking ownership. Multiple immutable references can exist at the same time, but they cannot modify the data.
- Mutable Borrowing (&mut T): You can borrow a reference to modify the data, but only one mutable reference can exist at any time. This prevents data races by ensuring exclusive access to the memory being changed.
**Rules for borrowing**:
- You cannot have simultaneous mutable and immutable references.
- You cannot use data after it has been moved or its ownership has been transferred.
- You cannot have multiple mutable references to the same data at once.

```rust
fn main() {
    let s1 = String::from("Hello"); // s1 owns the string
    let s2 = s1; // Ownership is moved from s1 to s2, s1 is no longer valid

    // println!("{}", s1); // This would cause a compile-time error

    let s3 = &s2; // Borrow s2 (immutable borrow)
    println!("{}", s3); // s2 is still valid here, and so is s3

    let s4 = &mut String::from("World");
    s4.push_str("!"); // s4 can modify the value since it's a mutable borrow
}
```
A **dangling reference** (or dangling pointer) occurs when a reference or pointer exists, but the data it points to has been deallocated or moved, meaning the reference is now pointing to invalid or freed memory.
```rust
fn main() {
    let r;              // Declare a reference
    {
        let x = 5;
        r = &x;         // Borrow x
        // x goes out of scope here
    }
    println!("{}", r); // Error: r is a dangling reference since x is out of scope
}

```
### Slicing

In Rust, slices are a way to reference a portion of a collection (such as an array or a string) without taking ownership of the data. When working with strings, a string slice (&str) allows you to borrow part of a string (or the entire string) without owning it, meaning you can use the slice without copying or modifying the original data.
```rust
let s = String::from("Hello, Rust!");
let slice = &s[7..11];  // "Rust"
```
- &s[..5] is the same as &s[0..5].
- &s[7..] is the same as &s[7..s.len()].
- &s[..] is a slice of the entire string.


## Structures
In Rust, a struct (short for structure) is a custom data type that allows you to group related values with different data types into a single unit. **Mutable Structs**, If you want to modify the fields of a struct after it is created, the instance must be mutable (mut keyword). You can create a new instance of a struct by copying some fields from an existing instance using struct update syntax(..instance_to _be _copied_from).

By default, Rust doesn’t know how to print structs using println!. However, you can enable debug printing by adding **#[derive(Debug)]** above your struct definition. Using **{:?}** prints the struct in debug format, while **{:#?}** provides a more readable, pretty-printed format.
```rust
#[derive(Debug)] 
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn main() {
    let mut user1 = User {
        username: String::from("user123"),
        email: String::from("user@example.com"),
        sign_in_count: 1,
        active: true,
    };

    let user2 = User {
    email: String::from("another@example.com"),
    ..user1  // Copies remaining fields from user1
    };

    println!("Username: {}", user1.username);
    user1.email = String::from("new_email@example.com");
    println!("Email: {}", user1.email);

    println!("{:#?}", user2); //printing a struct in more readable format 
}
```

### Tuple Structs
Rust also supports tuple structs, which look like tuples but are defined as structs. They don’t have named fields, just types, but they can be useful when you want to give a tuple a meaningful name. You access the fields of tuple structs by using dot notation and their index.
```rust
struct Color(i32, i32, i32);  // A tuple struct with three i32 fields
let black = Color(0, 0, 0);   // Create an instance
println!("Black color: {}, {}, {}", black.0, black.1, black.2);
```

### Methods on structs
Structs themselves don’t have methods, you can define methods for a struct using impl blocks. The **&self** parameter is a reference to the instance of the struct on which the method is called.

**Associated functions** in Rust are functions that are defined within an impl block for a struct but do not take self as a parameter. These functions are associated with the struct type itself, rather than with a specific instance of the struct. Associated functions are often used to create constructor functions that generate instances of the struct in a convenient way. These functions are called using the struct's name rather than an instance of the struct.

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Method to calculate the area of the rectangle
    fn area(&self) -> u32 {
        self.width * self.height
    }

    // An associated function that creates a square
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}

fn main() {
    
    let rect = Rectangle { width: 30, height: 50 };
    println!("The area of the rectangle is {} square pixels.", rect.area());

    // Create a square using the associated function
    let sq = Rectangle::square(30);
    println!("Square: {} x {}", sq.width, sq.height);
}

```

## Enums
In Rust, an enum (short for enumeration) is a powerful feature that allows you to define a type by enumerating its possible variants. You can create an instance of an enum by specifying one of its variants. Enum variants can hold data. Each enum variants can store different types of data, including tuples, structs, or even other enums.

Enum methods are similar to struct methods, and they are defined within an impl block associated with the enum. Methods on enums allow you to define behavior specific to that enum and its variants. You can define methods that act on the enum as a whole, or you can define methods that operate on specific variants of the enum.
```rust
enum Message {
    Quit,                       // No data
    Move { x: i32, y: i32 },    // Named fields like a struct
    Write(String),              // Holds a String
    ChangeColor(i32, i32, i32), // Holds a tuple of i32 values
}

impl Message {
    // A method that processes the message
    fn call(&self) {
        match self {
            Message::Quit => println!("Quit message"),
            Message::Move { x, y } => println!("Move to ({}, {})", x, y),
            Message::Write(text) => println!("Text: {}", text),
            Message::ChangeColor(r, g, b) => println!("Change color to ({}, {}, {})", r, g, b),
        }
    }
}

fn main(){

   let msg = Message::Move { x: 10, y: 20 };
   let text = Message::Write("Hello".to_string()); 
   msg.call(); 
   text.call();
}

```
### Match with enum
One of the main advantages of enums in Rust is the ability to use match expressions to handle different variants. The match expression here matches on the msg enum and executes different code depending on which variant is encountered.
```rust
    fn main() {
        let msg = Message::Move { x: 10, y: 20 };

        match msg {
            Message::Quit => println!("Quit message"),
            Message::Move { x, y } => println!("Move to ({}, {})", x, y),
            Message::Write(text) => println!("Text: {}", text),
            Message::ChangeColor(r, g, b) => println!("Color: ({}, {}, {})", r, g, b),
        }
    }

```
### if let in enum
if let and while let for Enums While match is the most common way to handle enums, Rust also provides the if let and while let constructs for simpler cases where you only care about one specific variant. This is a more concise way of handling cases when you only care about one enum variant.
```rust
let msg = Message::Write(String::from("Hello"));

if let Message::Write(text) = msg {
    println!("Message: {}", text);
}
```
### Option Enum
Enums with Option is a commonly used enums in Rust , which is built into the standard library:

**Option<T> Enum**
This is used for values that might be absent. It has two variants:
- Some(T) represents a value.
- None represents no value.

Sometimes, you might want to get the value inside an Option directly. You can use the unwrap() method to do this, but it’s unsafe because it will panic if the Option is None.
```rust
fn main(){

    let number = Some(5);
    let phrase = Some("raise hell!")
    let no_num: Option<i32> = None;

    println!("Some number: {}", number.unwrap());
    // Uncommenting the line below will cause a panic because no_number is None
    // println!("No number: {}", no_number.unwrap());
}
```rust
**Match with option enum**
fn main() {
    let some_number = Some(5);
    let no_number: Option<i32> = None;

    match some_number {
        Some(value) => println!("We have a value: {}", value),
        None => println!("No value"),
    }
}
```
**if let with option enum**
You can use if let to handle the Some case in a more concise way, especially when you only care about whether a value exists.
```rust
let some_number = Some(10);

if let Some(value) = some_number {
    println!("The number is: {}", value);
}
```
## Module System

### Crates
In Rust, a crate is the fundamental unit of compilation and package management. A crate can be a binary or a library, and Rust uses the term "crate" to refer to code packages that are either standalone applications (binaries) or reusable code libraries.
**Types**:
1. **Binary crate**: produces an executable program. It contains a main function, which is the entry point of the application.
    E.g main.rs file
    ```rust
    fn main() {
    println!("Hello, world!");
    }
    ```
2. **Library crate**: defines reusable code but does not produce an executable. It does not have a main function, but instead, it provides functions, types, and modules that other crates (or projects) can use.
    E.g lib.rs file
    ```rust
    pub fn greet() {
    println!("Hello from the library crate!");
    }
    ```

**Rules for crates**
- A package must have at least one crate.
- A package must have either 0 library crates or 1 library crate.
- A package can have any number of binary crates.

> If lib.rs is defined in the source directory rust will automatically create a library crate with the same name as your package and lib.rs will be the crate group. one can create this directly while creating the workspace by 
    ```
    cargo new --lib workspace_name
    ```
    a lib.rs is automatically created with a test module.

> In case of needed more binary crates one can create a directory named bin and in that folder can define required rust files, each file in this folder will represent a binary crate.

### Module
Modules in rust is a way to organize code into namespaces that help manage complexity and scope by allowing logical grouping of related functions, types, and traits. Modules also control visibility (public or private) and enable code reuse across different parts of a project.

Modules are defined using the ```mod``` keyword. You can define them inline within a file, or split them across multiple files for better organization.
Functions inside my_module are private by default, but you can use the pub keyword to make them public (accessible outside the module).

Modules can be nested inside other modules, which helps create a hierarchy of namespaces.
```super``` refers to the parent module (one level up). It is used to access items in the parent module from within a child (nested) module.

The ```use``` keyword allows you to bring items from a module into scope, so you don’t have to use the full path every time.

The ```self``` keyword refers to the current module. It is used to explicitly refer to items within the same scope (the current module), particularly when there might be ambiguity in module paths.

The ```as``` keyword is used for type casting and renaming imports. It provides a way to convert one type into another or to give a new name to an imported item to avoid name conflicts or for clarity.
```Rust
mod my_module {
    fn private_function() {
        println!("This is private");
    }
    pub fn public_function() {
        println!("This is public");
    }

    pub mod inner {
        pub fn hello() {
            super::public_function(); // Refers to my_module::public_function
            println!("Hello from inner module");
        }
        pub fn greet() {
        println!("hello, everyone");
    }


    }
}

fn main() {
    my_module::public_function(); // Accessible
    self::public_function();
    // my_module::private_function(); // Error: not accessible
    my_module::inner::hello();

    use my_module::inner;
    inner::greet();
    use my_module::inner::greet;
    greet();

    use my_module::public_function as my_print;
    my_print();
}
```

The ```use``` keyword can also be used to bring external dependencies into scope.
e.g adding a dependency through the cargo.toml file
```toml
[package]
name = "guessing_game"
version = "0.1.0"
edition = "2021"

[dependencies]
rand = "0.5.5"
colored = "2.0.0"
```
using ```use``` keyword in rust file to bring it in scope, nested paths can be used to include multiple files. To bring all items into from a dependency in a scope.
```rust
use rand::{Rng, CryptoRng, ErrorKind::Transient};
use std::io::* //bringing all items in std::io in a scope
fn main(){
    let secret_number = rand::thread_rng().gen_range(1,101);
}
```

**Splitting Modules into Different Files**
To use these external module files (foo.rs and bar/mod.rs), you declare them in main.rs using the mod keyword.
```
src/
├── main.rs       # Root file for a binary crate
├── foo.rs        # Module 'foo'
└── bar/
    └── mod.rs    # Module 'bar', stored in a subdirectory
```

```Rust
// main.rs
mod foo; // Brings in foo.rs as a module
mod bar; // Brings in bar/mod.rs as a module

fn main() {
    foo::hello(); // Using a function from foo module
    bar::greet(); // Using a function from bar module
}
```

##  Collections
In Rust, collections are data structures that can hold multiple values. They are part of Rust's standard library and offer more flexibility than arrays or tuples, which have a fixed size. Collections can grow or shrink dynamically, making them useful when you need a data structure with a variable size.

### Vectors

A ```Vec<T>``` is a growable array.
It stores elements of the same type, and its size can change dynamically.
Vectors store their data on the heap, meaning they can grow dynamically as long as there’s memory available.

- Creating a Vector

    ```rust
    //Create an empty vector and push elements to it later
    let mut v: Vec<i32> = Vec::new();
    v.push(1);
    v.push(2);
    v.push(3);
    // You can initialize a vector with values:
    let v = vec![1, 2, 3, 4];
    ```
- Accessing elements

    ```rust
    //using indexing; This can panic if index is out of bounds
    let v = vec![1, 2, 3, 4];
    println!("{}", v[2]); // Output: 3
    
    //using get method: returns an option, so it doesn't panic
    if let Some(third) = v.get(2) {
    println!("The third element is {}", third);
    } else {
        println!("There is no third element.");
    }
    ```
- Modifying Vectors

    ```rust
    let mut v = vec![1, 2];
    v.push(3); // v is now [1, 2, 3]
    
    let last = v.pop(); // last is Some(3), and v is now [1, 2]
    
    v[1] = 5; // v is now [1, 5]
    ```
- Iteration

    ```rust
    //immutable iteration
    let v = vec![1, 2, 3];
    for i in &v {
        println!("{}", i);
    }

    //mutable iteration
    let mut v = vec![1, 2, 3];
    for i in &mut v {
        *i += 1;
    }
    println!("{:?}", v); // Output: [2, 3, 4]
    ```
- Slicing Vectors

    ```rust
    let mut v = vec![1, 2, 3, 4, 5];
    let slice = &mut v[1..3]; // slice contains [2, 3]
    slice[0] = 10; // v is now [1, 10, 3, 4, 5]
    ```
- Other methods
    - ```len``` : Returns the number of elements in the vector.
    - ```is_empty()``` : Checks if the vector is empty.
    - ```clear()``` : Removes all elements from the vector.
    - ```extend()``` : Appends elements from another collection to the vector.


### Strings
A ```String``` is stored as a collection of UTF-8 encoded bytes allowing for efficient storage of multilingual text and can be resized or changed.
Since characters in UTF-8 encoding can take up multiple bytes which makes direct indexing into strings tricky, trying to index a string by position could lead to errors. Rust prevents this for safety reasons.

Rust provides two primary types for handling strings:
1.  String (growable, heap-allocated)
2.  &str (string slice, borrowed reference)


- Creating a string  
    ```rust
    let mut s1 = String::from("Hello");
    let mut s2  = "there".to_string();
    ```
- Appending/Concatenating a string
    ```rust
    s1.push_str(", world!"); // Appending to the string
    
    let s3 = s1 + &s2; // s1 (s1's ownership) is moved here, cannot be used after this

    let s3 = format!("{}{}", s1, s2); // Doesn't move ownership, so s1 & s2 still can be used.
    ```
- Iterating over strings
    
    Since strings are stored as UTF-8 encoded bytes, you can iterate over them by characters, bytes, or graphemes (complex characters in Unicode).
    ```rust
    let s = String::from("Hello");
    for c in s.chars() { // Iterate over characters
        println!("{}", c);
    }
    
    let s1 = String::from("नमस्ते");
    for b in s.bytes() {
        println!("{}", b); // Prints each byte value
    }

    ```
    A grapheme is a user-perceived character that can consist of one or more Unicode code points, such as the Hindi letter "स्" which is made up of the consonant "स" and the halant "्" (a diacritical mark that suppresses the inherent vowel).
    
    Iterating over grapheme clusters requires the use of an external crate, as the standard library doesn't directly provide this functionality.
    
    ```toml
    [dependencies]
    unicode-segmentation = "1.10.1"
    ```
    
    ```rust
    use unicode_segmentation::UnicodeSegmentation;
    fn main() {
        let s = "नमस्ते 🌍"; // Contains complex Unicode characters, including emoji

        // Iterate over grapheme clusters
        for grapheme in s.graphemes(true) {
            println!("{}", grapheme);
        }
    }
    ```

### Hash maps
A ```HashMap<K, V>``` stores key-value pairs, where K is the type of the keys and V is the type of the values.
HashMap provides efficient lookup based on keys.

- **Creating a HashMap**

    A HashMap can be created using the HashMap::new() method or with the collect method
- **Accessing values**

    You can retrieve a value from a HashMap using the ```get``` method, which takes a reference to the key and returns an Option<&V> (which will be Some(&V) if the key exists, or None if it does not).

- **Updating values**
    There are several ways:
    - Overwriting the Value for an Existing Key: Inserting a value with an existing key will replace the old value.

    - Inserting a Value Only If the Key Doesn’t Exist: Use ```entry()``` to insert a value for a key only if that key does not already have a value.
    
    - Updating a Value Based on the Old Value: You can modify the value by first accessing the entry and then updating it.
    
    ```rust
        use std::collections::HashMap;

    fn main() {
        // Create a new, empty HashMap
        let mut scores = HashMap::new();

        // Insert key-value pairs into the HashMap
        scores.insert(String::from("Blue"), 10);
        scores.insert(String::from("Red"), 50);

        println!("{:?}", scores);

        let score = scores.get("Blue"); //Accessing values 
        match score {
            Some(s) => println!("Score: {}", s),
            None => println!("No score found"),
        }

        scores.insert(String::from("Blue"), 25); // Overwrites the previous value

        scores.entry(String::from("Yellow")).or_insert(50); // Inserts 50
        scores.entry(String::from("Blue")).or_insert(100);  // Does nothing

        if let Some(score) = scores.get_mut("Blue") { //Updating a Value Based on the Old Value
        *score += 10;
        }
    }
    ```
- Iterating over HashMaps

    You can iterate over all the key-value pairs in a HashMap using a for loop.

    ```rust
     for (key, value) in &scores {
        println!("{}: {}", key, value);
    }
    ```

## Error handling

### Panic 
A panic occurs when the program encounters an unrecoverable error, such as accessing an out-of-bounds array index or attempting to unwrap an Option that is None. When a panic occurs, Rust unwinds the stack, cleaning up resources as it goes, and eventually terminates the program.
You can also manually trigger a panic using the ```panic!``` macro, providing a custom message:
```rust
fn main() {
    panic!("This is a custom panic message!");
}
```

**RUST_BACKTRACE** is an environment variable in Rust that controls the generation of backtraces when a program panics. When enabled, it provides detailed information about the call stack at the time of the panic, which is invaluable for debugging.
- Setting it to '1' will show a limited backtrace that includes the panic message and a few frames of the call stack.
    ```shell
    RUST_BACKTRACE=1 cargo run
    ```

- Setting it to 'full'  provides a complete backtrace with more detailed information about each frame, including the file names and line numbers.
    ```shell
    RUST_BACKTRACE=full cargo run
    ```

### Recoverable errors
Recoverable errors are those that do not cause the program to terminate abruptly.
For this purpose, rust provides two important types: Result and Option.

**Result<T, E>**

The Result type is used for functions that can return an error. It is an enum that has two variants:
- Ok(T): Represents a successful operation and contains a value of type T.
- Err(E): Represents a failure and contains an error value of type E.

In this example:

The ```read_username_from_file``` function returns a ```Result<String, io::Error>```, indicating it either returns a username or an ```io::Error```.

The ```?``` operator is used to propagate errors in functions that return Result. When you use the ? operator:

- If the result is Ok, it extracts the value.
- If it’s Err, it returns the error from the current function.


Here if ```File::open``` or ```read_to_string``` returns an Err, the error will be returned immediately from the function.

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut file = File::open("username.txt")?; // ? operator propagates errors
    let mut username = String::new();
    file.read_to_string(&mut username)?;
    Ok(username) // Return Ok with the username
}

fn main() {
    match read_username_from_file() {
        Ok(username) => println!("Username: {}", username),
        Err(e) => println!("Error reading username: {}", e),
    }
}
```

**```unwrap```** is a method that can be called on the Result enum to retrieve the value inside an ```Ok``` variant or cause a panic if it encounters an ```Err``` variant.

**```Expect```** method can be used to specify the error message that gets passed to the panic macro.

```rust
let result: Result<i32, &str> = Ok(42);
let value = result.expect("Failed to get the value"); // value is 42

let result_err: Result<i32, &str> = Err("An error occurred");
let value_err = result_err.expect("Expected an Ok value"); // This will panic with the message: "Expected an Ok value"
```

### Custom error types

You can define your own error types by creating an enum that implements the std::error::Error trait. This is useful for complex applications where you want to handle multiple error types.
```rust
use std::fmt;

#[derive(Debug)]
enum MyError {
    NotFound,
    PermissionDenied,
}

impl fmt::Display for MyError {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        match self {
            MyError::NotFound => write!(f, "Item not found"),
            MyError::PermissionDenied => write!(f, "Permission denied"),
        }
    }
}

impl std::error::Error for MyError {}

fn perform_action() -> Result<(), MyError> {
    // Simulate an error
    Err(MyError::NotFound)
}

fn main() {
    match perform_action() {
        Ok(_) => println!("Action performed successfully!"),
        Err(e) => println!("Error: {}", e),
    }
}
```


## Generic Types 

Generics in Rust allow you to write flexible, reusable code by enabling the definition of functions, structs, enums, and traits that can operate on multiple types without sacrificing type safety.

### Generic functions
 You can define functions that take parameters of any type. 
 This is done using angle brackets **(<>)** to specify the type parameters.

```rust
fn print_value<T>(value: T) {
    println!("{:?}", value);
}

fn main() {
    print_value(42);          // Works with an integer
    print_value("Hello");     // Works with a string
}
```
### Generic Structs / Enums

You can create structs that can hold values of any type. Similarly, enums can also be defined with generics.

```rust
struct Pair<T, U> { //here since there are 2 generic types described the variables can be of different datatypes as well.
    first: T,
    second: U,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}

fn main() {
    let pair = Pair { first: 1, second: "one" };
    println!("Pair: ({}, {})", pair.first, pair.second);

    let success = Result::Ok(42);
    let error: Result<i32, &str> = Result::Err("error occurred");
}
```

### Generic methods
In Rust, you can also use generics in methods, allowing methods of structs or enums to operate on types specified as parameters.
```rust
struct Pair<T, U> {
    first: T,
    second: U,
}

impl<T, U> Pair<T, U> {
    fn new(first: T, second: U) -> Self {
        Pair { first, second }
    }

    fn first(&self) -> &T {
        &self.first
    }

    fn second(&self) -> &U {
        &self.second
    }

    fn compare<F>(&self, func: F) -> bool
    where
        F: Fn(&T, &U) -> bool, // F must be a function that takes references to T and U
    {
        func(&self.first, &self.second)
    }
}

fn main() {
    let pair = Pair::new(5, "Hello");

    // Accessing the first and second values
    println!("First: {}", pair.first());
    println!("Second: {}", pair.second());

    // Comparing values using a closure
    let is_greater = pair.compare(|x, y| x > &10); // Compare the first value with 10
    println!("Is the first value greater than 10? {}", is_greater);
}
```

## Traits
Traits are a powerful way to define shared behavior across multiple types. Traits
define a set of methods that can be implemented for any type (structs, enums, etc.) Each type provides its own implementation for the methods required by the trait., allowing different types to share common behavior while still being distinct. 
It specifies what methods must be implemented, but not how.

Traits also provide default implementations for some or all of their methods. This allows types to use the default behavior or override it with a custom implementation.
You can write functions that accept parameters constrained by traits, meaning the function will work with any type that implements a specific trait.
 
```rust
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}

struct NewsArticle {
    headline: String,
    location: String,
    content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{} - {}", self.headline, self.location)
    }
}

fn notify(item: impl Summary) { //function that accepts parameters constrained by a trait.
    println!("Breaking news: {}", item.summarize());
}

struct Tweet {
    username: String,
    content: String,
}

impl Summary for Tweet {} //Default implementation
```

You can use traits to define the types of function parameters 
or return values by using the ```impl``` Trait syntax.

Rust also allows **dynamic dispatch** using trait objects. 
This is useful when you want a collection of different types 
that implement the same trait, or when the exact type isn't known 
at compile time. Trait objects are created using references like 
```&dyn``` Trait or ```Box<dyn Trait>```.

```rust
fn notify(item: impl Summary) { //traits as paramters
    println!("Breaking news: {}", item.summarize());
}


fn notify(item: &dyn Summary) { //dynamic dispatch using traits
    println!("Notification: {}", item.summarize());
}

fn returns_summarizable() -> impl Summary { //traits as return types
    Tweet {
        username: String::from("user"),
        content: String::from("This is a tweet."),
    }
}



let article = NewsArticle {
    headline: String::from("Headline"),
    location: String::from("Location"),
    content: String::from("Content"),
};

notify(&article); // Use a trait object (dynamic dispatch)

```

### Trait Bounds
Trait bounds are a way to specify that a generic type must implement one or more traits. 
This allows you to constrain the types that can be used in a function, struct, or method to those that implement the required behavior.

You can specify multiple traits for a type using the ```+``` syntax. 
```rust
use std::fmt::Display;

fn print_value<T: Display>(value: T) {
    println!("{}", value);
}

fn compare_and_print<T: Display + PartialOrd>(a: T, b: T) {
    if a > b {
        println!("{} is greater than {}", a, b);
    } else {
        println!("{} is less than or equal to {}", a, b);
    }
}

fn main() {
    print_value(42);       // Works because i32 implements Display
    print_value("Hello");  // Works because &str implements Display

    compare_and_print(10, 20);  // Works with i32 as it implements both Display and PartialOrd
    compare_and_print(3.5, 2.1); // Works with f64 too
}
```
Sometimes, if the number of trait bounds grows or the syntax 
becomes too complex, the where clause provides a cleaner alternative. 
The where clause separates the trait bounds from the function 
signature, improving readability.

```rust
fn compare_and_print<T>(a: T, b: T)
where
    T: Display + PartialOrd,
{
    if a > b {
        println!("{} is greater than {}", a, b);
    } else {
        println!("{} is less than or equal to {}", a, b);
    }
}
```

### Blanket implementations

Rust also supports blanket implementations, which allow you to 
implement a trait for all types that satisfy a particular trait 
bound. For example, you could  implement a trait for all types 
that implement another trait.

```rust
impl<T: Display> ToString for T {
    fn to_string(&self) -> String {
        format!("{}", self)
    }
}

/*This implements the ToString trait for any type T that 
implements Display. In effect, this is saying, "Any type 
that can be displayed can also be converted to a string."*/
```

## Lifetimes

In Rust, lifetimes are a mechanism for tracking how long references
are valid, ensuring memory safety without needing a garbage collector.
Rust’s ownership system manages memory via ownership, borrowing, and
lifetimes. Lifetimes help the compiler understand the scope of 
references and ensure that they don’t outlive the data they point to.

Lifetimes are annotated with a special syntax: ```'a```, ```'b```, etc., 
where ```'a``` represents a particular lifetime. Both x and y have 
the lifetime 'a, and the result has the same lifetime 'a. This 
guarantees that the returned reference is only valid as long as
both x and y are valid.

In the ```longest``` function, we return a reference to either ```x``` or ```y```, but the returned reference must not outlive either of the input references.
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn main() {
    let string1 = String::from("long string");
    let string2 = "short";
    
    let result = longest(&string1, &string2);
    println!("The longest string is {}", result);
}

fn main() {
    let string1 = String::from("long string");
    let result;
    {
        let string2 = String::from("short string");
        result = longest(&string1, &string2);
        // `string2` goes out of scope here, but `result` still references it
    }
    println!("The longest string is {}", result); // Error: `result` is referencing `string2`
}

// Lifetime in a struct
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");

    // Create an instance of ImportantExcerpt with a reference to the first sentence
    let excerpt = ImportantExcerpt {
        part: first_sentence,
    };

    // The struct can only hold the reference as long as 'novel' is valid
    println!("Excerpt: {}", excerpt.part);
}
```

### Lifetime Elision

In simple cases, Rust can infer lifetimes without explicit annotations 
through lifetime elision rules. The compiler applies these rules to 
reduce the need for writing lifetimes manually in straightforward 
cases. There are three basic elision rules:

- **Rule 1:** Each parameter that is a reference gets its own lifetime.
    *E.g:* ```fn foo(x: &i32)``` becomes ```fn foo<'a>(x: &'a i32)```.

- **Rule 2:** If there is exactly one input lifetime parameter, that 
    lifetime is assigned to all output references.
    *E.g:* ```fn foo(x: &i32) -> &i32``` becomes ```fn foo<'a>(x: &'a i32) -> &'a i32```

- **Rule 3:** If there are multiple input lifetime parameters, but 
    one of them is &self or &mut self (for methods), the lifetime 
    of self is assigned to all output references.
    E.g: ```fn foo(&self, x: &i32) -> &i32``` becomes 
    ```fn foo<'a>(&'a self, x: &'a i32) -> &'a i32```

### Static Lifetimes
The ```'static``` lifetime is a special lifetime that applies to 
references that live for the entire duration of the program. Data 
with the ```'static``` lifetime is stored in the program’s binary 
(for example, string literals).

Here, the string literal "I am a static string" has a 
'static lifetime because it is hardcoded into the program and 
will always be available throughout the program’s execution.
```rust
fn static_str() -> &'static str {
    "I am a static string"
}

fn main() {
    let s = static_str();
    println!("{}", s);
}
```

## Testing in Rust
Rust provides a powerful and built-in testing framework to write 
and run tests for code.  It ensures that your code behaves as 
expected, preventing bugs and regressions. This framework supports
writing unit tests, integration tests, and documentation tests, 
all with a focus on safety and correctness.

### Unit Testing

A simple unit test is written using the ```#[test]``` attribute 
before a function.

These macros are used to check conditions and will panic if the conditions are not
met. If a test panics, it is considered a failure.
- ```assert_eq!```(left, right) : checks if the left and right expressions are equal.

- ```assert_ne!```(left, right) : checks if they are not equal.

- ```assert!```(condition) : checks if the condition is true.

You can add custom failure messages to the assert!, assert_eq!, and
assert_ne! macros. 

```rust
// Function to be tested
fn add(a: i32, b: i32) -> i32 {
    a + b
}

// Unit tests
#[cfg(test)]
mod tests {
    // Import the outer function for testing
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5); // This will pass
    }

    #[test]
    fn test_add_negative() {
        //custom error message
        assert_eq!(add(-2,-3),-7, "Expected {} to be equal to {}", add(-2,-3),-2-3);
    }

    #[test]
    fn test_add_fail() {
        assert_ne!(add(2, 2), 5); // This will pass, 2 + 2 is not equal to 5
    }
}

```

To run tests, use the following command in the terminal:
```shell
cargo test
```
This command compiles and runs all tests in the project. By default,
Rust runs the tests in parallel, which speeds up testing for larger 
codebases.

### Integration testing
Integration tests are used to test how different parts of your code 
work together. They live in the tests directory at the root of your 
project and are meant to test the public API of your crate (library 
or binary). Unlike unit tests, integration tests are kept in separate 
files and do not have direct access to private functions or modules.
You can't directly test binary crates with integration tests, this is 
why it is common to see a binary crate as a wrapper around Library crate,
so that you can run integration tests.
```
project-root/
├── src/
│   └── lib.rs
└── tests/
    └── integration_test.rs
```
**In src/lib.rs:**
```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```
**In tests/integration_test.rs:**
No need of seperate test module
```rust
// Integration test for the `add` function
extern crate project_name;

#[test]
fn test_add() {
    assert_eq!(project_name::add(2, 3), 5);
}
```
**To run integration tests:** 

```cargo test --test integration_test```

### Should panic

The ```#[should_panic]``` test attribute in Rust is used to indicate that 
a particular test is expected to panic during its execution. If the
test function does not panic, the test will fail.

```rust

fn divide(a: i32, b: i32) -> i32 {
    if b == 0 {
        panic!("Division by zero is not allowed!");
    }
    a / b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic]
    fn test_divide_by_zero() {
        divide(1, 0);  // This should panic
    }
}
```
You can also specify an optional expected parameter to the 
```#[should_panic]``` attribute, which allows you to check that 
the panic message contains specific text. 

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic(expected = "Division by zero is not allowed!")]
    fn test_divide_by_zero() {
        divide(1, 0);  // This will panic with the specified message
    }
}
```
### Test options

1. **Run specific tests by name:**

    ```cargo test test_name```
    E.g ```cargo test test_add``` 

2. **Filter tests by substring:**

    You can run all tests that match a specific substring: ```cargo test substring```
     E.g : ```cargo test add``` for all those tests containing add in their name.
3. **Filter tests by module:**
    
    ```cargo test test::``` this will run all tests from module test.

4. **Run ignored tests:**
    If you've marked tests with #[ignore], you can run only the ignored tests using:
    ```rust
    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        #[ignore]
        fn expensive_test() {
            // A long-running test that you want to skip by default
        }
    }
    ```
    To ignore those tests
    ```cargo test -- --ignored```

5. **Disable output capture:** By default, Rust tests capture output (println! statements). To display output even for passing tests, use:
    
    ```cargo test -- --nocapture```

6. **Limit the number of test threads:**  You can limit the number 
    of threads running tests in parallel by specifying the 
    ```--test-threads``` option. This is helpful when you want to 
    reduce system load or debug race conditions.
    E.g: Run tests using only one thread.

    ```cargo test -- --test-threads=1```

7. Show more detailed output:
    To display verbose output of test execution, use:

    ```cargo test -- --test-threads=1 --nocapture --exact```

## Closures
closures in Rust are anonymous functions that can capture variables 
from their environment. They are similar to functions, but with 
additional flexibility, allowing them to access variables from 
the scope in which they were defined. 

```rust
let closure = |param1, param2| {
    // Body of the closure
    //if closure is a single expression, braces are optional
};

let x = 10;

let add_to_x = |y| x + y;  // `x` is captured by reference

println!("{}", add_to_x(5));  // Output: 15

```
### Refactoring using closures
Suppose you have multiple functions or code blocks that perform 
similar operations, but with minor differences. You can refactor 
the code by abstracting the common iteration and transformation 
logic into a reusable function that takes a closure as an 
argument to define the operation. In order to define structs, enums or 
functions paramters that use closures we need to use generics and
trait values.
Rust’s type system determines how variables are captured, and 
closures can be categorized into three traits:
- Fn: Captures variables by reference.
- FnMut: Captures variables by mutable reference.
- FnOnce: Captures variables by taking ownership (consumes the environment).

The function takes a vector and a closure f that specifies how to transform 
each element. This reduces duplication and allows for more 
flexible transformations.
```rust
//using functions
fn apply_to_elements<F>(v: &Vec<i32>, f: F) -> Vec<i32>
where
    F: Fn(i32) -> i32,
{
    v.iter().map(|&x| f(x)).collect()
}

//using structures
struct Action<F>
where
    F: Fn(&mut Robot),
{
    description: String,
    execute: F,
}

impl Robot {
    fn perform_named_action<F>(&mut self, action: Action<F>)
    where
        F: Fn(&mut Robot),
    {
        println!("Performing action: {}", action.description);
        (action.execute)(self);
    }
}
```

By default, closures borrow variables from their environment. 
If you want a closure to take ownership of captured variables, 
you can use the move keyword. This forces the closure to capture
variables by value. The move keyword is particularly useful when
passing closures to threads or asynchronous functions because 
these contexts often require ownership.
```rust
let s = String::from("hello");

let capture_move = move || {
    println!("{}", s);  // `s` is moved into the closure
};

// `s` is no longer accessible here because it has been moved

```
## Iterators
An iterator in Rust is any type that implements the Iterator 
trait, which provides a way to sequentially access the elements
of a collection or sequence.
```rust
    let numbers = vec![1, 2, 3, 4, 5];
    let mut iter = numbers.iter();  // Creates an iterator over the vector

    println!("{:?}", iter.next());  // Output: Some(1)
    println!("{:?}", iter.next());  // Output: Some(2)
```

The primary method of the Iterator trait is next(). 
Each call to next() yields the next item in the sequence, 
returning None when the iteration is complete.
The core trait is defined as,
```rust
pub trait Iterator {
    type Item; // The type of elements being iterated over

    // Returns the next element in the sequence, or `None` if iteration is finished
    fn next(&mut self) -> Option<Self::Item>;

    // Other methods are provided by default (like `map`, `filter`, `fold`, etc.)
}
```
### implementing Iterator Adapters
Iterators in Rust can be transformed and combined using 
iterator adapters. 

- **map()**: Transforms each element using a closure.
    ```rust
    let numbers = vec![1, 2, 3];
    let doubled: Vec<i32> = numbers.iter().map(|x| x * 2).collect();
    println!("{:?}", doubled);  // Output: [2, 4, 6]
    ```

- **filter()**: Filters elements based on a condition.
    ```rust
    let numbers = vec![1, 2, 3, 4, 5];
    let evens: Vec<i32> = numbers.iter().filter(|&&x| x % 2 == 0).collect();
    println!("{:?}", evens);  // Output: [2, 4]
    ```

- **enumerate()**: Adds an index to each element.
    ```rust
    let numbers = vec![10, 20, 30];
    for (index, value) in numbers.iter().enumerate() {
        println!("Index: {}, Value: {}", index, value);
    }
    ```

- **fold()**: Reduces the elements to a single value by 
    repeatedly applying a closure.
    ```rust
    let numbers = vec![1, 2, 3, 4];
    let sum = numbers.iter().fold(0, |acc, &x| acc + x);
    println!("{}", sum);  // Output: 10
    ```

### Implementing your own iterator

```rust
    struct EvenNumbers {
        current: usize,
        max: usize,
    }

    impl EvenNumbers {
        fn new(max: usize) -> Self {
            EvenNumbers { current: 0, max }
        }
    }

    impl Iterator for EvenNumbers {
        type Item = usize;

        fn next(&mut self) -> Option<Self::Item> {
            if self.current >= self.max {
                return None;
            }
            let even = self.current * 2;
            self.current += 1;
            Some(even)
        }
    }

    fn main() {
        let even_numbers = EvenNumbers::new(5);
        for num in even_numbers {
            println!("{}", num);  // Output: 0, 2, 4, 6, 8
        }
    }
```

## Publishing Rust Crates
### Build profiles
Cargo provides three predefined profiles:

- **dev**: Used when you run ```cargo build```. It’s optimized for fast
    compilation rather than runtime performance.
    - No optimizations: The default optimization level is 0.
    - Debug information: Debug information is included by default
    - Fast rebuilds.

- **release**: Used when you run ```cargo build --release```. It 
    sacrifices compile time for runtime performance.
    - Full optimizations: The default optimization level is 3, 
    which provides maximum optimizations.
    - Minimal debug information: Debug information is usually 
    disabled or minimal.
    - Link Time Optimization (LTO): Disabled by default, but can
    be enabled for further optimizations at the cost of slower 
    build times.

-  **test**: This profile is automatically used when you run 
    ```cargo test```. It’s similar to the dev profile, but can 
    be customized specifically for testing.

### Customizing Build profiles
You can override or customize these profiles in your 
project’s Cargo.toml file under the ```[profile.*]``` sections.

```toml
[profile.dev]
opt-level = 1      # Enable minimal optimization for faster runtime performance
debug = true       # Keep debug symbols for development
lto = false        # LTO is not necessary for development
overflow-checks = true   # Enable overflow checks during development

[profile.release]
opt-level = 3      # Maximize runtime performance
debug = false      # Disable debug information for smaller binary size
lto = true         # Enable Link Time Optimization for better performance
panic = 'abort'    # Panic behavior set to 'abort' to reduce binary size

```

### Documentation comments
In Rust, documentation comments are special types of comments 
used to generate API documentation for your code. 
***Rust's documentation comments support Markdown formatting.***

To generate the documentation, use the following command:
```cargo doc --open```
```cargo doc```: Generates documentation for your crate.
```--open```: Automatically opens the generated documentation in your web browser.

The generated documentation includes all items documented using
/// and //!, and it will be rendered as HTML.

Types :
-  **Line Documentation Comments (///)** :
    These are placed just above the item (such as a function, 
    struct, or module) they document.
    ```rust
    /// Adds two numbers together.
    ///
    /// # Arguments
    /// * `a` - The first number.
    /// * `b` - The second number.
    ///
    /// # Example
    /// ```
    /// let result = add(2, 3);
    /// assert_eq!(result, 5);
    /// ```
    fn add(a: i32, b: i32) -> i32 {
        a + b
    }
    ```

- **Inner Documentation Comments (//!)** :
    Inner documentation comments are typically used to document 
    entire crates, modules, or files. They are placed inside a 
    module or crate and usually at the top of the file.
    //! This is the main module of the application.
//! It contains the primary logic for starting the application.
    ```rust
        mod app {
            //! This module handles application-specific logic.
            //!
            //! It contains helper functions for managing state and control flow.
        }
    ```

#### Sections in Documentation
- Example: Shows an example of how to use the item.
    ```rust
    /// # Example
    /// ```
    /// let result = add(5, 10);
    /// assert_eq!(result, 15);
    /// ```
    ```
- Arguments: Lists the arguments the function takes, often with a brief explanation.
    ```rust
    /// # Arguments
    /// * `a` - The first number.
    /// * `b` - The second number.
    ```
- Panics: Describes situations where the function might panic.
    ```rust
    /// # Panics
    /// This function will panic if the denominator is zero.
    ```
- Errors: Describes the errors that might be returned by the function.
    ```rust
    /// # Errors
    /// Returns an error if the file cannot be opened.
    ```
- Safety: For unsafe functions, this section should explain the conditions under which the function is safe to call.
    ```rust
    /// # Safety
    /// The caller must ensure that the provided pointer is valid.
    ```


> Sometimes, you want to expose part of your API in a simpler way by re-exporting modules, types, or functions. This is especially useful for larger crates where you want users to access common functionality without going deep into module hierarchies.
```
    // Re-exporting the entire math module under the root.
    pub use crate::math;
```

> Now, users can access add and multiply directly from the crate root,
```rust
    // Before:
    use my_crate::math::add;
    // After:
    use my_crate::add;
```

> Running Tests in Documentation : Rust’s rustdoc allows you to write code examples inside your 
documentation comments and automatically test them.
```shell
    cargo test
```

### Step-by-Step Guide to Publishing a Crate on Crates.io

1. **Log into crates.io with GitHub:**
   - Go to [crates.io](https://crates.io) and sign in using your GitHub account.

2. **Generate a New API Token:**
   - Once logged in, click the drop-down menu next to your profile icon and go to **Account Settings**.
   - Scroll down to the **API Access** section and select **New Token**.
   - Assign a name to the token (e.g., "crate publishing token") and click **Create**.
   - This generates a login token. **Copy the token**.

3. **Log in to Cargo:**
   - Open your terminal and use the following command to log in:
     ```bash
     cargo login <your-token>
     ```
   - Replace `<your-token>` with the token you copied from crates.io.
   - If the login is successful, you are now ready to publish your crate.

4. **Prepare Your Crate for Publishing:**
   - Open your `Cargo.toml` file and verify the metadata.
     - Ensure the crate **name** is unique (it must not conflict with any existing crate name on crates.io).
     - Add a **description** and **license** to your crate.
     - Make sure your **email address is verified** on crates.io.

5. **Commit All Files to Git:**
   - Ensure all relevant project files are committed to Git. This step is important if you plan to maintain version control.

6. **Publish the Crate:**
   - Once everything is set up, you can publish your crate using the following command:
     ```bash
     cargo publish
     ```
   - This uploads your crate to crates.io.

7. **Publish New Versions of Your Crate:**
   - When you want to release a new version, update the version number in your `Cargo.toml` (according to semantic versioning).
   - Run:
     ```bash
     cargo publish
     ```
   - This publishes the updated version of your crate.

8. **Deprecating a Version (Yank):**
   - If you want to prevent a specific version of your crate from being downloaded (but still keep it in history), you can use the **yank** command:
     ```bash
     cargo yank --vers 0.1.0
     ```
   - To undo this action, you can run:
     ```bash
     cargo yank --vers 0.1.0 --undo
     ```

## Cargo Workspaces


ChatGPT said:
ChatGPT
Cargo workspaces in Rust are a powerful feature that allows you to manage multiple related packages (crates) within a single repository. This feature is particularly useful for projects that consist of several interdependent crates, as it simplifies dependency management and enhances project organization. 

Packages (crates) in a cargo workspace share the same Cargo.lock file and output directory.The root of the workspace contains a ````Cargo.toml``` file that specifies the workspace members. 

```toml
    [workspace]
    members = [
        "crate_a",
        "crate_b",
        "crate_c",
    ]
```

Each crate (package) in a workspace has its own Cargo.toml file, which defines its metadata, dependencies, and settings.
```bash
    my_workspace/
    ├── Cargo.toml      # Workspace manifest
    ├── crate_a/
    │   └── Cargo.toml  # Crate A manifest
    ├── crate_b/
    │   └── Cargo.toml  # Crate B manifest
    └── crate_c/
        └── Cargo.toml  # Crate C manifest

```

When developing interdependent crates, you can use relative paths in the dependencies. For example, if crate_b depends on crate_a, you can specify it like this in crate_b/Cargo.toml:
```toml
    [dependencies]
    crate_a = { path = "../crate_a" }
```
 You can run a specific crate by navigating to its directory and using ```cargo run```.
 ```toml
cargo run -p crate_a
 ```

 ### Installing binaries from crates.io
One can only install packages which have a binary target.
Packages can have binary and library targets.
```bash
cargo install ripgrep
```
All binaries installed with cargo install are stored in the installation routes bin directory. 
```bash
~/.cargo/bin/<binary_name>
```
You can run it with ```~/.cargo/bin/rg```

To run installed binaries without specifying the full path, you can add Cargo's bin directory to your PATH
```bash
export PATH="$HOME/.cargo/bin:$PATH"
```
To apply changes ```source ~/.bashrc```

To update all installed binaries
```bash
cargo install-update -a
```
To uninstall a binary
```bash
cargo uninstall <crate_name>
```
## Smart Pointers

Smart pointers in Rust are advanced data types that manage memory and provide additional features beyond regular pointers. Unlike standard pointers, smart pointers automatically handle memory allocation and deallocation, helping prevent memory leaks and ensuring safety. 

### Box Pointers
Box pointers (commonly referred to simply as "boxes") are a smart pointer type that enables heap allocation of values. They provide a way to create a single ownership model, ensuring that the data they point to has a unique owner. 
```rust
let b = Box::new(5); // `b` is a Box containing an integer
```
**Using boxes for making a linked list**
```rust
enum List {
    // `Cons` variant holds an integer and a boxed pointer to the next List element.
    Cons(i32, Box<List>),
    // `Nil` variant represents the end of the list (an empty list).
    Nil,
}
// Import the `Cons` and `Nil` variants for easier access.
use List::{Cons, Nil};
// Main function where the execution starts.
fn main() {
    // Create a linked list: 1 -> 2 -> 3 -> Nil
    let list: List = Cons(1,Box::new(Cons(2,Box::new(Cons(3, Box::new(Nil))))));
}
```
### Deref Trait
The ```Deref``` trait in Rust allows smart pointers (or other types) to behave like regular references by overloading the dereference operator (*). The Deref trait allows types like ```Box<T>, Rc<T>, Arc<T>```, and even user-defined types to behave like references. The Deref trait defines how the * operator behaves when applied to a smart pointer. It converts a smart pointer into a reference to the inner type (T) so you can access the data it points to as if it were a regular reference.

```rust
    pub trait Deref {
        type Target: ?Sized;  // The type that this smart pointer dereferences to
        fn deref(&self) -> &Self::Target;
    }

    let x = Box::new(5);
    println!("{}", *x); // Dereferences the Box to get the value 5
```

Rust has a feature called ```deref coercion``` that automatically converts a smart pointer into a reference when needed. For example, if a function expects a &str (a reference to a string slice) but you pass it a String, Rust will automatically call deref() to convert the String into a &str.
```rust
fn takes_str(s: &str) {
    println!("String slice: {}", s);
}
fn main() {
    let my_string = String::from("Hello, Rust!");
    takes_str(&my_string);
}
```
**Custom Deref Implementation**
```rust
use std::ops::Deref;

// Define a custom smart pointer.
struct MyBox<T>(T);

// Implement the `Deref` trait for `MyBox<T>`.
impl<T> Deref for MyBox<T> {
    type Target = T;  // The inner value is of type `T`
    fn deref(&self) -> &T {
        &self.0  // Return a reference to the inner value
    }
}

fn main() {
    let x = 5;
    let y = MyBox(x);
    // Using dereference (`*`) operator to access the value inside `MyBox`.
    println!("y = {}", *y);  // Deref to get the value 5
}
```
**DerefMut**: This is the mutable version of Deref. It allows mutable dereferencing, letting you modify the inner value.

```rust
use std::ops::{Deref, DerefMut};

// Define the same custom smart pointer `MyBox`.
struct MyBox<T>(T);

// Implement the `Deref` trait for `MyBox<T>`.
impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.0
    }
}

// Implement the `DerefMut` trait for `MyBox<T>`.
impl<T> DerefMut for MyBox<T> {
    fn deref_mut(&mut self) -> &mut T {
        &mut self.0
    }
}

fn main() {
    let mut x = MyBox(5);
    *x = 10;  // Mutably dereference `MyBox` to change the inner value.
    println!("x = {}", *x);  // Output: x = 10
}

```

### Drop Trait
The Drop trait in Rust is used to define custom behavior when a value goes out of scope. It's essentially Rust’s mechanism for performing cleanup when an object is no longer needed. This method is automatically called when the object goes out of scope, and you can customize it to perform specific cleanup tasks. 
```rust
struct CustomSmartPointer {
    data: String,
}

// Implement the Drop trait for CustomSmartPointer
impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer with data: `{}`", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer {
        data: String::from("my stuff"),
    };

    let d = CustomSmartPointer {
        data: String::from("other stuff"),
    };

    // When `c` and `d` go out of scope at the end of `main`, their `drop` methods are called automatically.
    println!("CustomSmartPointers created.");
}

```
You don’t need to explicitly call the drop method. Rust automatically calls drop for any object when it goes out of scope.
If you need to drop a value early (before the end of its scope), you can use the std::mem::drop function:
```rust
    let c = CustomSmartPointer {
    data: String::from("some data"),
    };
    println!("Dropping `c` early...");
    drop(c);  // Calls the `drop` method immediately
    println!("`c` has been dropped.");
```

### Reference Counting
In Rust, reference counting is a way to manage memory for data that can have multiple owners.

**Rc<T> (Reference Counted)**: This smart pointer is used for single-threaded scenarios where multiple parts of the program need to own and share access to the same data. It keeps track of the number of references to a piece of data and automatically cleans up when no references remain.

**Arc<T> (Atomic Reference Counted)**: Similar to Rc, but thread-safe for concurrent access. Arc uses atomic operations to safely update the reference count across threads, making it suitable for multi-threaded scenarios.

1. When you create a new Rc<T> or Arc<T> instance, Rust initializes a reference count set to 1.
2. Every time you clone an Rc or Arc, the reference count is incremented, reflecting that there is now an additional owner.
3. When an Rc or Arc goes out of scope (or is dropped), Rust automatically decrements the count.
4. Once the count reaches zero, Rust knows no one is using the data anymore, so it cleans up the memory.

```rust
use std::rc::Rc;

fn main() {
    let a = Rc::new(5);
    println!("Reference count after creating a: {}", Rc::strong_count(&a)); // 1

    let b = Rc::clone(&a);
    println!("Reference count after cloning a into b: {}", Rc::strong_count(&a)); // 2

    {
        let c = Rc::clone(&a);
        println!("Reference count after cloning a into c: {}", Rc::strong_count(&a)); // 3
    }

    // `c` goes out of scope here
    println!("Reference count after c goes out of scope: {}", Rc::strong_count(&a)); // 2
}

```

### Interior Mutability
Interior Mutability is a concept in Rust that allows modifying data even when there are immutable references to it. This pattern is essential when working with certain Rust smart pointers because Rust’s strict ownership model typically prevents mutating data through shared references (&T). However, with interior mutability, you can work around this by using specific smart pointers that enable mutation while upholding Rust’s guarantees.

**Rust provides three primary smart pointers that implement interior mutability:**

1. **Cell<T>** allows for copy-type values (values that implement the Copy trait) to be modified even when they’re accessed through an immutable reference.It achieves this by moving values in and out of the cell rather than returning references to the data directly, making it more restrictive in what data it can store. Cell<T> does not allow borrowing its contents; instead, you replace (set) and retrieve (get) the values directly, which works because Copy types can be duplicated trivially. 

```rust
use std::cell::Cell;

fn main() {
    let data = Cell::new(10);
    
    data.set(20); // Modify through an immutable reference
    println!("The value is: {}", data.get());
}
```

2. **RefCell<T>** is used for single-threaded scenarios and allows mutable access to its content through runtime borrow checking. While Rust enforces borrowing rules at compile-time, RefCell enforces borrowing at runtime. This means RefCell allows:
Multiple immutable borrows.
A single mutable borrow.
But it panics at runtime if these rules are violated.

```rust
use std::cell::RefCell;

fn main() {
    let data = RefCell::new(vec![1, 2, 3]);

    // Borrow as immutable (can have multiple immutable borrows)
    let borrowed_data = data.borrow();
    println!("Data: {:?}", *borrowed_data);

    // Mutable borrow (only one mutable borrow allowed)
    data.borrow_mut().push(4); // Modifies through an immutable reference to `data`
    println!("Data after mutation: {:?}", *data.borrow());
}
```

3. **Mutex<T> and RwLock<T>** allow for interior mutability with thread safety for multithreaded scenarios.
    
    - **Mutex<T>:** Provides exclusive, safe access to the data. It only allows one thread to access the data at a time by locking the data, blocking other threads until the lock is released.

    - **RwLock<T>:** Allows multiple readers or a single writer. This is beneficial when you have a high number of read operations compared to write operations.

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let data = Arc::new(Mutex::new(5));

    let handles: Vec<_> = (0..10).map(|_| {
        let data = Arc::clone(&data);
        thread::spawn(move || {
            let mut num = data.lock().unwrap();
            *num += 1; // Safely modify through the lock
        })
    }).collect();

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *data.lock().unwrap());
}
```

### Reference Cycles
Reference cycles occur when two or more values refer to each other in a circular way, preventing Rust from being able to clean up the memory they occupy. This problem primarily arises with reference-counted smart pointers like Rc and Arc, which manage memory by counting references. When reference cycles form, the reference count never drops to zero, resulting in a memory leak since the cycle of references prevents the data from being deallocated.

*Example*
- We have two structs, Node and Edge. Each Node can have a reference to another Node (say in a linked list structure).
- Using Rc<T> enables nodes to have multiple owners (e.g., shared references in the graph).
- However, if one node points back to another in a cycle, the reference count never reaches zero.
- node1 and node2 each hold a reference to each other, creating a reference cycle. Since each Rc has a reference count of at least 1 due to the cycle, Rust’s memory cleanup (i.e., drop check) never gets triggered for these nodes, resulting in a memory leak.

```rust
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug)]
struct Node {
    value: i32,
    next: Option<Rc<RefCell<Node>>>,
}

fn main() {
    let node1 = Rc::new(RefCell::new(Node {
        value: 1,
        next: None,
    }));

    let node2 = Rc::new(RefCell::new(Node {
        value: 2,
        next: Some(node1.clone()), // node2 points to node1
    }));

    node1.borrow_mut().next = Some(node2.clone()); // node1 points back to node2, creating a cycle

    // Check reference counts
    println!("node1 ref count: {}", Rc::strong_count(&node1)); // 2
    println!("node2 ref count: {}", Rc::strong_count(&node2)); // 2

    // At this point, both node1 and node2 have reference counts > 0, creating a cycle
}

```

To avoid reference cycles, Rust provides ```Weak<T>```, a version of Rc that does not increase the reference count. A Weak reference allows access to the data without taking ownership, thereby preventing cycles.
A Weak reference can be created from an Rc by calling ```.downgrade()```. Unlike Rc, Weak does not increment the reference count. Thus, the value can be cleaned up when all Rc references are gone, even if Weak references still exist. To access the data, Weak pointers need to be upgraded to Rc. This operation is fallible and returns None if the data has already been deallocated.

```rust
use std::cell::RefCell;
use std::rc::{Rc, Weak};

#[derive(Debug)]
struct Node {
    value: i32,
    next: Option<Rc<RefCell<Node>>>,
    prev: Option<Weak<RefCell<Node>>>, // Use Weak to avoid cycles
}

fn main() {
    let node1 = Rc::new(RefCell::new(Node {
        value: 1,
        next: None,
        prev: None,
    }));

    let node2 = Rc::new(RefCell::new(Node {
        value: 2,
        next: Some(node1.clone()),
        prev: None,
    }));

    node1.borrow_mut().prev = Some(Rc::downgrade(&node2)); // node1 has a weak reference to node2

    // Check reference counts
    println!("node1 strong count: {}", Rc::strong_count(&node1)); // 1
    println!("node1 weak count: {}", Rc::weak_count(&node1)); // 1

    println!("node2 strong count: {}", Rc::strong_count(&node2)); // 1
    println!("node2 weak count: {}", Rc::weak_count(&node2)); // 0

    // Now, even though node1 and node2 reference each other, node1's weak reference to node2
    // prevents a cycle, so memory can be cleaned up when strong counts drop to zero.
}
```

```node1``` and ```node2``` still reference each other, but node1’s reference to node2 is a Weak reference.
Since Weak does not increment the reference count, Rust can deallocate the nodes once the Rc counts drop to zero, preventing a memory leak.

> Strong Count: Counts the number of owning references (Rc or Arc clones) to the data. When the strong count reaches zero, the data is deallocated.

> Weak Count: Counts the number of non-owning references (Weak<T>) to the data. Weak references do not prevent deallocation. If only weak references remain (and the strong count is zero), the data is dropped, but the Weak pointers can still exist, they just won’t be able to access the data.

## Concurrency
Different approaches to implementing concurrency in programming languages and operating systems:

**Green Threads**:
- Green threads are managed by a runtime or virtual machine (like a language's standard library) rather than the operating system.
They allow multiple threads to run within a single OS thread and are scheduled by the language runtime.
- They're lightweight and can switch context faster, making them efficient for tasks that frequently yield control (like I/O-bound operations).
- They don’t fully utilize multicore systems since they're limited to a single OS thread.

**1-to-1 Thread Mapping**:
- In this model, each user thread is mapped to a separate OS thread. Threads are managed and scheduled by the OS, meaning they can run in parallel on multiple cores, taking full advantage of multicore processors.
- True parallelism is achieved, and the OS handles scheduling, balancing threads across cores.
- OS threads are heavier, with more overhead on context switching and memory usage.

Rust’s standard library offers a ```std::thread``` module to spawn and manage threads. The ```thread::spawn``` function creates a new thread and runs a given closure on it. The main thread can wait for other threads to finish by calling ```.join()```, which blocks the main thread until the spawned thread completes.

```rust
    use std::thread;

    let handle = thread::spawn(|| {
        println!("Hello from a new thread!");
    });

    handle.join().unwrap();  // Wait for the thread to finish

```
In this example, the ```handle.join().unwrap()``` call blocks the main thread until the spawned thread completes. This way, the main thread can synchronize and wait for all threads to finish before proceeding.

```rust
    use std::thread;

    fn main() {
        let handle = thread::spawn(|| {
            println!("Hello from the spawned thread!");
        });

        // The main thread waits here for the spawned thread to finish
        handle.join().unwrap(); 
        println!("Spawned thread has completed.");
    }

```

A ```move closure``` is a type of closure that captures variables from its environment by moving them into the closure rather than borrowing them. This is useful when you’re spawning threads, as Rust’s ownership model enforces strict rules about borrowing across threads. The ```drop``` function is used to manually release resources or perform cleanup operations when an object goes out of scope. 

```rust
    use std::thread;
use std::mem::drop;

fn main() {
    let mut handles = vec![];

    for i in 0..5 {
        // Each thread gets its own `i` value, so we use `move` to capture `i` by value
        let handle = thread::spawn(move || {
            println!("Thread {} started", i);

            // Explicitly drop `i` after using it
            drop(i);
            println!("Thread has dropped its value.");

            println!("Thread completed.");
        });

        // Push each handle to the vector
        handles.push(handle);
    }

    // Join all threads to ensure they complete before the main thread exits
    for handle in handles {
        handle.join().unwrap();
    }

    println!("All threads have completed.");
}
```
### Message Passing

Message passing is a concurrency model where threads communicate by sending messages to each other, rather than sharing memory. Rust implements message passing using channels provided by the std::sync::mpsc (multiple producer, single consumer) module. A channel can have multiple senders, but only one receiver by default. This setup is called multiple producer, single consumer (mpsc). Channels are a safe and efficient way to exchange data between threads.

```rust
    use std::sync::mpsc;
    use std::thread;
    use std::time::Duration;

    fn main() {
        let (tx, rx) = mpsc::channel();

        // Spawn a thread to send multiple messages
        thread::spawn(move || {
            let messages = vec!["Hello", "from", "the", "thread"];
            for msg in messages {
                tx.send(msg).unwrap();
                thread::sleep(Duration::from_millis(500));
            }
        });

        // Main thread receives each message as it becomes available
        for received in rx {
            println!("Received: {}", received);
        }
    }

```
Using multiple threads to send messages to a single receiver by cloning the sender. Each cloned sender acts as a new independent sender that shares access to the same channel.

```rust
    
    use std::sync::mpsc;
    use std::thread;

    fn main() {
        let (tx, rx) = mpsc::channel();

        for i in 0..5 {
            let tx = tx.clone();  // Clone the sender for each thread
            thread::spawn(move || {
                tx.send(format!("Message from thread {}", i)).unwrap();
            });
        }

        // Receiving messages from all threads
        for received in rx {
            println!("Received: {}", received);
        }
    }
```

Few essential methods for channels in Rust:
- **send()**: Sends data from the sender to the receiver. It returns a Result that indicates whether the message was sent successfully.
- **recv()**: Blocks until a message is received and returns a Result.
- **try_recv()**: Non-blocking version of recv(). It immediately returns a Result, either with a message or with an error if there’s no message.
- **iter()**: Allows iterating over the receiver until the channel is closed. This is useful for loops that read all available messages until all senders go out of scope.

### Sharing state

Sharing state between threads allows multiple threads to access or modify common data. Rust provides smart pointers and synchronization primitives like Arc and Mutex to safely share data in a multithreaded context. Rust’s ownership model enforces that each piece of data can only have one owner at a time. When sharing data between threads, Rust requires either.

**Arc (atomic reference counting)** is a thread-safe version of Rc (reference counting). Arc enables multiple threads to share ownership of data, and it keeps track of how many owners (references) there are. When the last reference goes out of scope, Arc cleans up the data. ```Arc: Arc::clone(&shared_data)``` increases the reference count. Each thread can then access the data in a read-only fashion since the vector is immutable.
``` rust
    use std::sync::Arc;
    use std::thread;

    fn main() {
        let shared_data = Arc::new(vec![1, 2, 3, 4, 5]);

        let mut handles = vec![];

        for _ in 0..3 {
            let shared_data = Arc::clone(&shared_data); // Clone an Arc pointer for each thread

            let handle = thread::spawn(move || {
                println!("{:?}", shared_data);
            });

            handles.push(handle);
        }

        for handle in handles {
            handle.join().unwrap();
        }
    }

```


**Mutex** provides mutual exclusion, meaning it allows only one thread at a time to access or modify the data it protects. By wrapping data in a Mutex, you ensure that only one thread can lock and access the data at a time, preventing race conditions.

Here ```Arc``` enables multiple threads to own the same ```Mutex```. Each thread can clone the Arc to get a new reference. ```counter.lock().unwrap()``` acquires the lock. If another thread holds the lock, ```lock()``` blocks until it is released. After locking, the thread has exclusive access to the data, allowing it to modify the counter safely.

``` rust
    use std::sync::{Arc, Mutex};
    use std::thread;

    fn main() {
        let counter = Arc::new(Mutex::new(0)); // Shared, thread-safe counter

        let mut handles = vec![];

        for _ in 0..10 {
            let counter = Arc::clone(&counter); // Clone `Arc` for each thread

            let handle = thread::spawn(move || {
                let mut num = counter.lock().unwrap(); // Lock `Mutex` to get exclusive access
                *num += 1; // Safely increment counter
            });

            handles.push(handle);
        }

        for handle in handles {
            handle.join().unwrap();
        }

        println!("Result: {}", *counter.lock().unwrap());
    }
```

**RwLock (Read-Write Lock)** allows multiple readers or one writer. This is useful when you want more flexibility than Mutex for read-heavy workloads.
    - Readers: Multiple threads can read the data concurrently if no writer holds the lock.
    - Writer: Only one thread can write, and it will block other readers and writers until it finishes.

```rust
    use std::sync::{Arc, RwLock};
    use std::thread;

    fn main() {
        let data = Arc::new(RwLock::new(vec![1, 2, 3]));

        let data_reader = Arc::clone(&data);
        let reader_handle = thread::spawn(move || {
            let data = data_reader.read().unwrap();
            println!("Read data: {:?}", *data);
        });

        let data_writer = Arc::clone(&data);
        let writer_handle = thread::spawn(move || {
            let mut data = data_writer.write().unwrap();
            data.push(4);
            println!("Updated data: {:?}", *data);
        });

        reader_handle.join().unwrap();
        writer_handle.join().unwrap();
    }
```

**Handling Deadlocks and Poisoned Locks**
Rust’s locks (Mutex and RwLock) can be poisoned if a thread panics while holding the lock, leaving it in an unusable state. When a lock is poisoned, lock() or read()/write() return a Result::Err, indicating the lock’s state might be inconsistent. You can recover from this by handling the error and reinitializing the data if needed.  Deadlocks can occur if multiple threads wait on each other’s locks. 

To avoid deadlocks:
- Only lock what’s necessary and release locks as soon as possible. 
- Avoid holding multiple locks in inconsistent order across threads.

## Object Oriented Programming

Object-Oriented Programming (OOP) in Rust might seem unconventional at first glance since Rust doesn’t have traditional classes, inheritance, or some of the mechanisms seen in languages like C++ or Java. However, Rust provides powerful abstractions to achieve the core principles of OOP — encapsulation, polymorphism, and inheritance 

### Encapsulation 
In OOP, encapsulation means bundling data (fields) and methods that operate on the data into a single unit. In Rust, encapsulation is achieved through structs and impl blocks:
**Structs** : Structs can be public or private, public structs expose their fields (attributes), while private structs limit access.
**Impl Blocka** : Implementation blocks help define functions (including constructors) that operate on the data within a struct.
``` rust
    // Defining a struct with private fields
    pub struct Rectangle {
        width: u32,
        height: u32,
    }

    // Associated methods (impl block) for Rectangle struct
    impl Rectangle {
        // Constructor method for Rectangle
        pub fn new(width: u32, height: u32) -> Self {
            Self { width, height }
        }

        // Public method that computes the area
        pub fn area(&self) -> u32 {
            self.width * self.height
        }

        // Getter for width, demonstrates controlled access
        pub fn width(&self) -> u32 {
            self.width
        }
    }

```

### Polymorphism 
Rust achieves polymorphism (the ability for different types to be used interchangeably) through traits. Trait objects enable dynamic dispatch for trait-based polymorphism. This allows you to write code that can operate on different types that implement a specific trait without knowing those types at compile time.  Unlike regular trait implementations, which use static dispatch (resolved at compile time), trait objects defer method selection until runtime, making them particularly useful when you need a collection of different types that share a common behavior.

``` rust
// Define a trait named Drawable with a single method `draw`
trait Drawable {
    fn draw(&self); // `draw` method to be implemented by any type that wants to be "Drawable"
}

// Define a struct `Circle` with a `radius` field
struct Circle {
    radius: f64,
}

// Define a struct `Square` with a `side` field
struct Square {
    side: f64,
}

// Implement the `Drawable` trait for the `Circle` struct
impl Drawable for Circle {
    fn draw(&self) {
        // Method prints a message specific to Circle
        println!("Drawing a circle with radius {}", self.radius);
    }
}

// Implement the `Drawable` trait for the `Square` struct
impl Drawable for Square {
    fn draw(&self) {
        // Method prints a message specific to Square
        println!("Drawing a square with side {}", self.side);
    }
}
```
**Using Trait Objects in Collections**
```rust
    fn main() {
    // Create a vector `shapes` to hold different types that implement `Drawable`
    // `Box<dyn Drawable>` is a trait object, allowing for dynamic dispatch
    let shapes: Vec<Box<dyn Drawable>> = vec![
        Box::new(Circle { radius: 5.0 }), // Boxed Circle stored as `Box<dyn Drawable>`
        Box::new(Square { side: 3.0 }),   // Boxed Square stored as `Box<dyn Drawable>`
    ];

    // Iterate over each shape in the `shapes` vector
    for shape in shapes {
        // Call the `draw` method on each shape
        // Dynamic dispatch occurs here, as Rust determines at runtime
        // which `draw` method (Circle's or Square's) to call
        shape.draw();
    }
}
```
### Inheritance 
Rust doesn't support inheritance directly, favoring composition and traits instead. 
**Composition** : Instead of subclassing, you can use composition by including instances of other structs or types as fields within a struct.

``` rust
    struct Display {
        width: u32,
        height: u32,
    }

    struct Screen {
        display: Display,  // Composing Screen with Display
        brightness: u8,
    }
```

**Trait Bounds and Trait Inheritance** : Rust enables a form of "inheritance" through trait bounds and trait inheritance. Traits can depend on other traits, allowing the creation of more complex behaviors.

```rust
// Define a base trait
trait Drawable {
    fn draw(&self);
}

// Define a trait that extends Drawable
trait Movable: Drawable {
    fn move_by(&mut self, x: i32, y: i32);
}

struct Player {
    x: i32,
    y: i32,
}

impl Drawable for Player {
    fn draw(&self) {
        println!("Drawing player at ({}, {})", self.x, self.y);
    }
}

impl Movable for Player {
    fn move_by(&mut self, x: i32, y: i32) {
        self.x += x;
        self.y += y;
    }
}

```
### Static and Dynamic dispatch
Dispatch refers to the process of determining which function to call when a method is invoked. Rust supports two main types of dispatch: static dispatch (or compile-time dispatch) and dynamic dispatch (or runtime dispatch).

| Feature             | Static Dispatch                          | Dynamic Dispatch                       |
|---------------------|------------------------------------------|----------------------------------------|
| **Method Resolution** | At compile-time                        | At runtime                             |
| **Performance**     | Faster due to compile-time resolution    | Slower due to runtime vtable lookup    |
| **Memory Usage**    | Potentially higher (code bloat)          | Typically lower due to shared vtable   |
| **Inlining**        | Possible                                 | Not possible                           |
| **Type Requirement**| Requires knowing types at compile time   | Types can vary, resolved at runtime    |
| **Flexibility**     | Less flexible, type-specific             | More flexible, allows polymorphism     |
| **Typical Use Case**| Generic programming, performance-critical| Trait objects, heterogeneous collections |
| **Code Size**       | Larger due to multiple specialized instances | Smaller, one instance with vtable lookup |
| **Error Checking**  | Errors caught at compile time            | Some errors may only appear at runtime |


```rust
// Define a trait with a single method
trait Shape {
    fn area(&self) -> f64;
}

// Implement Shape trait for Circle
struct Circle {
    radius: f64,
}

impl Shape for Circle {
    fn area(&self) -> f64 {
        3.14159 * self.radius * self.radius
    }
}

// Implement Shape trait for Square
struct Square {
    side: f64,
}

impl Shape for Square {
    fn area(&self) -> f64 {
        self.side * self.side
    }
}

// Function that uses static dispatch (generic function)
fn print_area_static<T: Shape>(shape: &T) {
    println!("Static dispatch area: {}", shape.area());
}

// Function that uses dynamic dispatch (trait object)
fn print_area_dynamic(shape: &dyn Shape) {
    println!("Dynamic dispatch area: {}", shape.area());
}

fn main() {
    let circle = Circle { radius: 5.0 };
    let square = Square { side: 3.0 };

    // Static dispatch
    print_area_static(&circle); // Compile-time determination for Circle
    print_area_static(&square); // Compile-time determination for Square

    // Dynamic dispatch
    let shapes: Vec<&dyn Shape> = vec![&circle, &square];
    for shape in shapes {
        print_area_dynamic(shape); // Runtime determination for each shape
    }
}

```

### State Design Pattern 

The State design pattern is a behavioral design pattern that allows an object to change its behavior when its internal state changes. The object will appear to change its class. In Rust, this pattern is commonly implemented using traits and enums to encapsulate state-specific behavior, allowing a struct to change behavior dynamically based on its current state. This is particularly useful for managing complex state transitions in systems with clear states, such as a finite state machine.

In the State pattern:
1. State-Specific Behavior: Each state has its own behavior, often implemented as a separate struct or enum variant.
2. Delegation to State: The context (main struct) delegates behavior to the current state.
3. State Transitions: The state can transition to another state, updating the context’s current state.

```rust
// Define a trait for states, with behaviors that change depending on the state
trait State {
    fn add_text(&self, text: &str, document: &mut Document);
    fn request_review(self: Box<Self>) -> Box<dyn State>;
    fn approve(self: Box<Self>) -> Box<dyn State>;
}

// Context struct that changes behavior based on its state
struct Document {
    content: String,
    state: Box<dyn State>, // Current state as a trait object
}

impl Document {
    // Create a new Document in the Draft state
    fn new() -> Document {
        Document {
            content: String::new(),
            state: Box::new(Draft), // Initial state
        }
    }

    // Delegate add_text behavior to the current state
    fn add_text(&mut self, text: &str) {
        self.state.add_text(text, self);
    }

    // Transition to Review state
    fn request_review(&mut self) {
        // Consume current state and transition to Review
        self.state = self.state.request_review();
    }

    // Transition to Approved state (Publish)
    fn approve(&mut self) {
        // Consume current state and transition to Published
        self.state = self.state.approve();
    }

    // Retrieve content if document is in Published state
    fn content(&self) -> &str {
        if self.state.is::<Published>() {
            &self.content
        } else {
            ""
        }
    }
}

// Define each state struct and implement state-specific behaviors
struct Draft;
struct Review;
struct Published;

// Implement the State trait for the Draft state
impl State for Draft {
    fn add_text(&self, text: &str, document: &mut Document) {
        document.content.push_str(text);
    }

    fn request_review(self: Box<Self>) -> Box<dyn State> {
        Box::new(Review) // Transition to Review
    }

    fn approve(self: Box<Self>) -> Box<dyn State> {
        self // Stay in Draft (can't approve a draft directly)
    }
}

// Implement the State trait for the Review state
impl State for Review {
    fn add_text(&self, _text: &str, _document: &mut Document) {
        // No-op: can't add text in Review state
    }

    fn request_review(self: Box<Self>) -> Box<dyn State> {
        self // Stay in Review
    }

    fn approve(self: Box<Self>) -> Box<dyn State> {
        Box::new(Published) // Transition to Published
    }
}

// Implement the State trait for the Published state
impl State for Published {
    fn add_text(&self, _text: &str, _document: &mut Document) {
        // No-op: can't add text in Published state
    }

    fn request_review(self: Box<Self>) -> Box<dyn State> {
        self // Stay in Published
    }

    fn approve(self: Box<Self>) -> Box<dyn State> {
        self // Stay in Published
    }
}

fn main() {
    let mut doc = Document::new();

    // In Draft state, we can add text
    doc.add_text("This is the first draft.");
    println!("Content in Draft: {}", doc.content); // Shows content

    // Transition to Review state
    doc.request_review();
    doc.add_text(" Additional text."); // No effect in Review state
    println!("Content in Review: {}", doc.content); // Content unchanged

    // Transition to Published state
    doc.approve();
    println!("Content in Published: {}", doc.content()); // Shows final content

    // Attempting to add text after publishing has no effect
    doc.add_text(" New text.");
    println!("Content after published: {}", doc.content()); // Unchanged content
}

```

## Patterns and Matching
Patterns allow you to match and deconstruct complex data types in a concise and readable way. The primary tool for pattern matching in Rust is the match expression, but patterns can also be used in if let expressions, while let loops, for loops, function parameters, and even variable declarations.

### Match Statement
A match statement compares a value against a series of patterns and executes the code associated with the first matching pattern.

```rust
fn main() {
    let number = 7;

    match number {
        1 => println!("One"),
        2 => println!("Two"),
        3 | 4 | 5 => println!("Three, Four, or Five"), // Multiple patterns with |
        6..=10 => println!("Between six and ten"),    // Range pattern
        _ => println!("Some other number"),           // Wildcard pattern
    }
}
```

### Pattern Matching with Enums
Enums are often used with match because each variant can represent a unique case. 

``` rust
enum Shape {
    Circle { radius: f64 },
    Rectangle { width: f64, height: f64 },
}

fn area(shape: Shape) -> f64 {
    match shape {
        Shape::Circle { radius } => 3.14159 * radius * radius,
        Shape::Rectangle { width, height } => width * height,
    }
}

fn main() {
    let circle = Shape::Circle { radius: 2.5 };
    println!("Area: {}", area(circle));
}

```

### if let and while let Conditional Matching
Enums are often used with match because each variant can represent a unique case. 

``` rust
fn main() {
    let some_value = Some(5);

    // if let for optional pattern matching
    if let Some(x) = some_value {
        println!("Found a value: {}", x);
    } else {
        println!("Found nothing");
    }

    // while let for repeated conditional matching
    let mut stack = vec![1, 2, 3];
    while let Some(top) = stack.pop() {
        println!("Popped: {}", top);
    }
}

```


###  Destructuring in Function Parameters and let Bindings
Patterns can also be used directly in let bindings and function parameters, allowing you to unpack complex structures when binding variables.

``` rust
struct Point {
    x: i32,
    y: i32,
}

fn print_coordinates(Point { x, y }: Point) {
    println!("Current location: ({}, {})", x, y);
}

fn main() {
    let (x, y) = (1, 2); // Tuple destructuring
    println!("x: {}, y: {}", x, y);

    let point = (3, 4);
    let (a, b) = point; // Destructuring to unpack `point`
    println!("a: {}, b: {}", a, b);

    let point = Point { x: 10, y: 20 };
    print_coordinates(point);
}

```

### Pattern Matching with Option and Result
Pattern guards provide additional conditions to refine matches. Guards use if statements to add an extra condition.
Nested patterns allow for matching complex data structures, such as enums inside structs or nested tuples.

``` rust
    fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("Cannot divide by zero".to_string())
    } else {
        Ok(a / b)
    }
    }

    fn main() {
    let value = Some(3);

    match value {
        Some(x) => println!("Got: {}", x),
        None => println!("No value"),
    }

    let result = divide(10.0, 2.0);

    match result {
        Ok(value) => println!("Result: {}", value),
        Err(message) => println!("Error: {}", message),
    }
}

```


### Advanced Patterns: Guards and Nested Patterns
@ bindings allow you to bind a value to a variable while still matching against a pattern. This is useful if you need to refer to the entire matched value as well as its parts.

``` rust
    enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(Color),
    }

    enum Color {
        RGB(i32, i32, i32),
        HSV(i32, i32, i32),
    }
    fn main() {
    let number = Some(4);

    match number {
        Some(x) if x < 5 => println!("Less than five: {}", x),
        Some(x) => println!("Greater or equal to five: {}", x),
        None => println!("No value"),
    }

        let msg = Message::ChangeColor(Color::RGB(0, 160, 255));

    match msg {
        Message::ChangeColor(Color::RGB(r, g, b)) => {
            println!("Change color to RGB: ({}, {}, {})", r, g, b);
        }
        Message::ChangeColor(Color::HSV(h, s, v)) => {
            println!("Change color to HSV: ({}, {}, {})", h, s, v);
        }
        _ => println!("Other message"),
    }
}

```

### Using @ Bindings
``` rust
fn main() {
    let number = Some(5);

    match number {
        Some(n @ 1..=5) => println!("Matched a range element: {}", n),
        Some(n) => println!("Got a value: {}", n),
        None => println!("No value"),
    }
}

```

## Unsafe Rust
Writing unsafe Rust is an advanced aspect of the Rust programming language that allows developers to perform operations that the Rust compiler cannot guarantee are safe. Unsafe Rust allows you to opt out of Rust's strict safety guarantees, giving you more control over memory and other low-level operations.

This is accomplished through the use of the unsafe keyword, which indicates that you are taking responsibility for upholding safety guarantees that the Rust compiler cannot enforce.

Unsafe Rust can be used for several critical operations:
1. **Dereferencing Raw Pointers**: Accessing memory directly through raw pointers.
```rust
fn main() {
    let mut value: i32 = 42;
    let r: *mut i32 = &mut value; // Create a mutable raw pointer

    unsafe {
        println!("Value before: {}", *r); // Dereference the raw pointer
        *r = 100; // Modify the value through the raw pointer
    }

    println!("Original value: {}", value); // Should print 100
}
```
2. **Calling Unsafe Functions**: Invoking functions that are marked as unsafe.
```rust
unsafe fn dangerous() {
    println!("This is an unsafe function!");
}

fn main() {
    unsafe {
        dangerous(); // Call the unsafe function
    }
}
```
3. **Accessing Unsafe Traits**: Using traits that require unsafe implementations.
```rust
unsafe trait MyUnsafeTrait {
    fn do_something(&self);
}

struct MyStruct;

unsafe impl MyUnsafeTrait for MyStruct {
    fn do_something(&self) {
        println!("Doing something unsafely!");
    }
}

fn main() {
    let my_struct = MyStruct;
    unsafe {
        my_struct.do_something(); // Call the unsafe trait method
    }
}
```
4. **Manipulating Mutable Static Variables**: Working with mutable static variables that are shared across threads.
```rust
static mut GLOBAL: i32 = 0;

fn increment() {
    unsafe {
        GLOBAL += 1; // Increment the global variable
    }
}

fn main() {
    increment();
    unsafe {
        println!("Global value: {}", GLOBAL); // Print the value
    }
}
```
5. **Using unsafe Blocks**: Enclosing code that performs unsafe operations within an unsafe block.
```rust
fn main() {
    let x = 42;
    let r = &x as *const i32; // Create a raw pointer

    unsafe {
        println!("Raw pointer points to: {}", *r); // Dereferencing a raw pointer
    }
}
```

### Extern
The ```extern``` keyword is used to define an interface for interacting with foreign (non-Rust) code, primarily when working with C libraries or other languages. It allows Rust programs to call functions and use data types defined in other programming languages. Calling external functions requires unsafe blocks, as the Rust compiler cannot guarantee the safety of operations performed through foreign interfaces.

1. **Extern Functions**
When you want to call functions from an external library, you declare them using the extern keyword.  

```rust
extern "C" {
    fn c_function(x: i32) -> i32; // Declare an external C function
}

fn main() {
    unsafe {
        let result = c_function(5); // Call the external function within an unsafe block
        println!("Result from C function: {}", result);
    }
}

```
You can group multiple external function declarations within an extern block for better organization:
```rust
extern "C" {
    fn c_function_one(x: i32) -> i32;
    fn c_function_two(y: f64) -> f64;
}
```
2. **FFI (Foreign Function Interface)**
extern is primarily used in the context of FFI, which allows Rust to call functions and use data types defined in other languages, particularly C.
```C 
// c_library.c
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}
```
You can call this function from Rust like this:
```rust
// main.rs
#[link(name = "c_library")] // Link with the C library
extern "C" {
    fn add(a: i32, b: i32) -> i32; // Declare the C function
}

fn main() {
    unsafe {
        let sum = add(3, 4); // Call the C function
        println!("Sum: {}", sum); // Prints "Sum: 7"
    }
}

```
3. **Extern Crates and Extern Types**
The extern keyword is also used to declare external crates in Rust, which are libraries published on crates.io.
```rust
extern crate serde; // Declare an external crate

use serde::{Serialize, Deserialize}; // Use items from the serde crate

fn main() {
    // Your code that uses the serde library
}
```

You can also declare types that are defined externally, typically used in FFI contexts.
```rust
extern {
    type ExternalType; // Declare an external type
}
```