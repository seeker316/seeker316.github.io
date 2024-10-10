# Rust

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


