# Introduction
Rust's borrow checker is an excellent example of a difference with a purpose. The borrow checker is a unique feature within Rust that promotes safe coding practices by enforcing rules related to the single ownership principal.  
Learning to work with the borrow checker, not against it, is invaluable.  
## Functional Programming
Functions are the essential building blocks of the languages.  
A function can even perform operations on other functions, described as a higher-order function.  
## Expression Oriented
Most operations are expressions that return a value, instead of statements that return nothing. Expression-oriented programming is a close cousin of functional programming.
```rust
fn factorial(n:i32) -> i32 {
    match n {
        0..=1 => 1,
        _ => n * factorial(n-1)
    }
}
```
## Pattern Oriented
Pattern matching is often associated with switch statements.
```rust
fn display_firstname(name:(&str, &str)) {
    match name {
        (_, first) => println!("{}", first),
    }
}
```
## Safeness
Immutable variables, the single ownership principle..
## Ownership
- Bob drives the car.
- Bob lends the car to Ari. Ari drives the car. When Ari is done, he returns the car to Bob.
- Bob drives the car.
## Lifetimes
Lifetimes is a feature in Rust that prevents accessing values that are no longer available. A reference is a basic pointer in Rust.  
The benefit of the lifetimes feature is a stable memory environment without the worry of handing references.  
## Fearless Concurrency
## Zero-Cost Abstraction
Garbage collection can be costly and nondeterministic. Ownership, provides deterministic memory management without overhead.

# Install
## rustup
https://rustup.rs  

```shell
cargo --version
rustc --version
```
## Hello World
```rust
fn main() {
    println!("Hello, world!");
}
```
## Compile and Run
Rust is an ahead-of-time compiled language where a crate compiles to a true binary, not an intermediate language requiring a virtual machine for execution.  
Once compiled, Rust binaries can probably execute anywhere else, even where Rust is not installed.  
```
rustc hello.rs
```
## Cargo
cargo is in lieu of using the rustc compiler directly.   
cargo can create and manage packages, compile binaries, maintain a secure environment, and manage dependencies.  
Rustaceans often prefer cargo, instead of rustc, for compilation.  
```
mkdir helloworld
cd helloworld
cargo init
cargo build
cargo run
```
## Main Function
The main function is the entry point function for executable crates. The main function prototype has no parameters and returns either a unit or the Termination trait.  
In Rust, a unit is an empty tuple (). This is the default return.  
From the main function, you can return an integer value to the operating system using the Termination trait. libc::EXIT_SUCCESS/libc::EXIT_FAILURE(1/0).  
Instead of returning from main, you can call the exit function(std::process::exit) to prematurely exit an application. However, the exit function immediately terminates the application without performing cleanup for the functions currently on the call stack, possibly preventing an orderly shutdown.
## Command-Line Arguments
```
myapp arg1 arg2
cargo run arg1 arg2
```
Command-line arguments are provided as input  to the main function in many languages. However, the main function in Rust has no parameters. Command-line arguments are instead read programmatically using the std::env::args function.
```rust
fn main() {
    if let Some(arg)=std::env::args().nth(0) {
        println!("{}", arg);
    }
    for arg in std::env::args() {
        println!("{}", arg);
    }
    let all_arg: String=std::env::args().collect();
    println!("{}", all_arg);
}
```
## Evcxr REPL
```
rustup component add rust-src
cargo install --locked evcxr_repl
evcxr
```
```shell
>> use std::mem;
>> mem::size_of::<usize>()
8
>> i8::MAX
127
>>
```

# Variables
The type system impacts every aspect of the programming language.  
By default, variables are immutable.  
## type, variable, memory
Memory is where data resides. Variables are named memory storage, and they remove the need to reference data using memory addresses. Memory has no intrinsic format. A type describes the memory layout of a value, such as an integer or float value.  
Variable binding is another import term in the Rust type system. A declaration, such as the let statement, creates a binding between the variable name and a memory location.(Memory locations are bound to variables)  
The type for the variable is set explicitly within the let statement or via type inference.
## Integer Types
isize,i8,i16,i32,i64,i128  
usize,u8,u16,u32,u64,u128  
```
>> let mut number = i8::MAX;
>> number
127
>> number.overflowing_add(1)
(-128, true)
>>
```
## Base
```rust
println!("{}", 10); // 10
println!("{:04b}", 0b10); // 0010
println!("{}", 0o12); // 10
println!("{}", 0xA); // 10
```
## Floating Point Types
Rust has single- and double-precision primitive types. Each type consists of a sign, exponent, and mantissa component.  
The f32 type is for single-precision numbers and the f64 type represents double-precision numbers. For type inference, the default floating point type if f64.
```rust
use std::f64::consts;
let radius=4.234;
let diameter=2.0*radius;
let area=consts::PI*radius.powf(2.0);
println!("{} {} {}", radius, diameter, area);
```
Neither float types, f32 or f64, are ideal for fixed-point numbers. Use rust_decimal crate.
```
>> :dep rust_decimal = "1.30"
>> use rust_decimal::Decimal;
>> let num1 = Decimal::from_str("2.5").unwrap();
>> let num2 = Decimal::new(75, 1); // 7.5
>> println!("{}", num1+num2); // 10.0
```
## Numeric Ranges
i8 -128 to 127  
u8 0 to 255
u16 0 to 65535
## Casting
```rust
let var1 = 1 as char;
let var2:f32 =  123.45;
let var3 = var2 as f64;
let var4 = 1.23 as u8 as char;
```
Double cast is allowed.  
let condition:bool = true;  
Internally, bool values are bitwise values 0x01 for true and 0x00 for false.
## Char
The char type is for individual characters, a Unicode character.
```rust
let var1 = 'a';
let var2:char = 'b';
let var3 = 65 as char; // 'A'
let var4 = 'A' as i32; // 65
```
## Pointers
Rust has two categories of pointers: references and raw pointers. References are safe pointers.  
It is important to recognize that raw pointers, references, and regular values are different types.  
i32 and &i32 are different types. The i32 type refers to a 32-bit integer value. However, &i32 refers to a reference that has a safe pointer to an i32 value. &i32 is different from a *i32 type, which is a raw pointer type.  
Pointers are first-class citizens in the Rust language and have the same capabilities as any other type. Pointers can be used as variables, structure fields, function parameters, or return values.  
## References
References borrow the value at the referenced memory location.  
You can reference a value using the ampersand operator &. In this context, you can interpret the ampersand as "get the reference of".  
The dereference operator(*) dereferences a reference. You will receive the value at the memory address that is referenced.
```rust
let aref:&i32 = &5;
let value:i32 = *aref;
let ref1 = &15;
let ref2 = &20;
let value1 = ref1 + 10;
let value2 = ref1 * ref2;
println!("{} {}", value1, value2); // 25 300
```
For a reference, the value is automatically dereference before performing a mathematical calculation.  
The == operator compares the value at a reference, not the memory location. If you want to compare the actual memory addresses, call the eq function, in the std::ptr module.  
```rust
let num_of_eggs = 10;
let num_of_pizza = 10;
let eggs = &num_of_eggs;
let pizza = &num_of_pizza;
eggs == pizza  // true
ptr::eq(eggs, pizza)  // false
```
## Operators
Mathematical operators: + - * / %  
Boolean operators: == < > <= >= !=  
Logical operators: && || !   
Bitwise operators: & | ^ << >>

# Strings
Rust strings adhere to the Unicode Standard with UTF-8 encoding. The primary string types in Rust are String and str.
## str
The str type has all the characteristics of a slice, including being unsized and read-only. Because str is a slice, you normally borrow a str, &str. A str consists of two fields: pointer to the string data and length.  
&'static str: notation for a string literal including the lifetime.  
## String
The String type, is implemented as a specialized vector that consists of character values. Strings can be mutable and are growable. Like a vector, the String type consists of three fields: pointer to a backing array, length, and capacity.  
There are different approaches to creating new String types. such as String::from and str::to_string.
```rust
let string_1 = String::from("Alabama");
let string_2 = "Alaska".to_string();
let mut string_3 = String::new();
string_3.push_str("Arizona");
// create a String from Unicode characters
let vec_1 = vec![65,114,107,97,110,115,97,115];
let string_4 = String::from_utf8(vec_1).unwrap();  // "Arkansas"
```
## Length
What is the length of a specific Unicode string?  
- ASCII characters are a single byte in size.
- Greek characters are 2 bytes in size.
- Chinese characters are 3 bytes in size.
- Emojis are 4 bytes in size.

The len function return the number of bytes in the string.
```rust
let english = "Hello".to_string();
let greek = "γεια".to_string();
let chinese = "你好".to_string();
println!("English {}: Bytes {} Length {}", english, english.len(), english.chars().count());
println!("Greek {}: Bytes {} Length {}", greek, greek.len(), greek.chars().count());
println!("Chinese {}: Bytes {} Length {}", chinese, chinese.len(), chinese.chars().count());
```
## Extending a String
You can extend the value of a String, but not a str type.  
push, push_str, insert, insert_str  
```rust
let mut alphabet = "a".to_string();
alphabet.push('b');  // ab
let mut numbers = "one".to_string();
numbers.push_str(" two");  // one two

let mut greeting = "Hello".to_string();
let salutation = " Sir".to_string();
greeting = greeting + &salutation;
println!("{}", greeting);

let mut characters = "ac".to_string();
characters.insert(1, 'b');
let mut numbers = "one three".to_string();
numbers.insert_str(3, " two");
```
## Capacity
As a specialized vector, Strings have a backing array and a capacity. Capacity is the size of the backing array, while length is the current size of the String.  
Knowing the required capacity upfront enables a more efficient version. 
```rust
let mut string_1 = String::with_capacity(9);
string_1.push('快');
println!("Capacity {} Length {}", string_1.capacity(), string_1.len()); // 9 9
string_1.push('乐');
println!("Capacity {} Length {}", string_1.capacity(), string_1.len()); // 9 9
string_1.push_str("的");
println!("Capacity {} Length {}", string_1.capacity(), string_1.len()); // 9 9
```
Initially, we set the capacity large enough, during execution, the backing array is not reallocated and performance is improved. 
## Accessing a String Value
```rust
let string_1 = "hello".to_string();
let character = string_1[1]; // ERROR
```
Does the index refer to the byte or character position? Accessing an individual character with an index is not allowed.  
You can access characters in a String using a String slice. string[starting index..ending index]
```rust
let string_1 = "快乐的".to_string();
let slice = &string_1[3..=5];
println!("{:?}", slice); // 乐 
```
## String Characters
Iterating all characters is sometimes useful. .chars()
```
>> "快乐的".chars().nth(2).unwrap()
'的'
```
## Deref Coercion
You can substitute a borrowed String, &String, anywhere &str is expected.  
The String type implements the deref trait for str.
```rust
fn FuncA(str_1: &str) {
    println!("{}", str_1);
}
fn main() {
    let string_1 = "Hello".to_string();
    FuncA(&string_1);
}
```
## Formatted String
```
>> let left = 5;
>> let right = 10;
>> format!("{left} + {right} = {result}", result=left+right)
```
## Help Functions
- clear: Erases a String but does not reduce the current capacity.
- contains: contains<'a, P>(&'a self, pat: P)
- ends_with
- eq_ignore_ascii_case
- replace
- split
- starts_with
- to_uppercase

```
let mut string_1 = "something ".to_string();
string_1.clear();
string_1.shrink_to_fit();
string_1.is_empty()

let string_2 = "this is fun.".to_string();
string_2.contains("fun")

let string_3 = "going to Topeka".to_string();
string_3.ends_with("Topeka")

"One".eq_ignore_ascii_case("ONE")

let string_6 = "Bob went shopping; then Bob went home.".to_string();
string_6.replace("Bob", "Alice")

let string_7 = "The magic of words";
string_7.split(" ")

let string_8 = "Sydney is scenic.".to_string();
string_8.starts_with("Sydney")

"Cool!".to_uppercase()
```

# Console
Both print! and println! macros insert a formatted string into the standard output stream (stdout). The first parameter of each macro is a format string and a string literal. 
## Padding, Alignment, and Precision
You can fine-tune the format specification after a:(colon) character in the placeholder, {:format}.  
You set the padding, or the column width, for a placeholder using the {:width} syntax. The default alignment: numerical values: right; strings: left.  
```rust
let numbers = [("one", 10), ("two", 2000), ("three", 400)];
println!("{:7}{:10}", "Text", "Value");
println!("{:7}{:10}", "====", "=====");
for (name, value) in numbers {
    println!("{:7}{:<10}", name, value); // number align left
}
```
For floating point numbers, the syntax is padding.precision.
```rust
let (f1, f2) = (1.2, 3.45678);
println!("Result: {:<10.2} {:.4}", f1, f2);
```
## Base
```rust
println!("Default     {}", 42);
println!("Binary      {:b}", 42);
println!("Octal       {:o}", 42);
println!("Hexadecimal {:x}", 42);
println!("HEXADECIMAL {:X}", 42);
```
## Developer Facing
The public-facing types implement the Display trait for the {} placeholder. Other types are considered "developer facing" and use the {:?} placeholder. 
```rust
let vec1 = vec![1,2,3];
println!("{:?}", vec1);
```
User-defined types do not implement either the Display or Debug trait. You should add #\[derive(Debug)\]
```rust
#[derive(Debug)]
struct Planet<'a> {
    name: &'a str,
    radius: f64,
}
fn main() {
    let earth = Planet {
        name: "Earth",
        radius: 3958.8,
    };
    // Planet {name: "Earth", radius: 3958.8}
    println!("{:?}", earth);
    // println!("{:#?}", earth);
}
```
## Write! Macro
The print! and println! macros display to standard output. The write! macro is more flexible and can forward formatted strings to different targets that implement either the fmt::Write or io::Write trait.
## Console Read and Write
The std::io module provides helpful functions to access the console.  
stdout, stdin, stderr  
For reading input from the console, Stdin implements the BufRead and Read traits.  
- read: Reads input into a byte buffer
- read_line: Reads a line of input into a String buffer
- read_to_string: Reads input to EOF into a String buffer

# Control Flow
## if
```rust
let city = "Honolulu";
let is_new_yark = "New York City" == city;
if is_new_york {
    println!("Welcome to NYC");
} else {
    println!("Not NYC");
}
```
The if let expression is a variation to the if. If the pattern matches, the related block is executed. Otherwise, the block is skipped.
```rust
enum Font {
    Name(String),
    Default,
}
let font = Font::Name("Arial".to_string());
if let Font::Name(chosen) = font {
    println!("{} font selected", chosen);
} else {
    println!("Use default font");
}
```
For an if, the if block can return a value. The default return is the empty tuple (). All the blocks within an if, including else and else if, must return a consistent type. The return value can be used to initialize a new variable, an assignment, or be part of a larger expression.
## while
```rust
let mut count = 5;
let mut result = 1;
while count > 1 {
    result = result * count;
    count = count - 1;
}
```
while let is a variation of while.
```rust
let mut count = Some(5);
let mut result:i32 = 1;
while let Some(value) = count { // 如果 count 是 Some, 则继续循环
    if value == 1 { //当 value 为 1 时, 将 count 置为 None 结束循环
        count = None;
    } else {
        result = result * value;
        count = Some(value-1);
    }
} 
```
## break and continue
```rust
let mut i = 0;
while i < 10 {
    i = i + 1;
    if i % 2 == 0 {
        continue;
    }
    println!("{}", i);
}
```
## for
```rust
for value in 1..=5 {
    println!("{}", value);
}
for (idx,value) in (1..=10).enumerate() {
    println!("{}: {}", idx, value);
}
```
Attempting to use the enumerate function with the vector will not work. The implementation of the IntroIterator trait does not provide an implementation of this function. Use iter, iter_mut, or into_iter first.
```rust
let values = vec![1,2,3];
for item in values.iter().enumerate() {
    println!("{:?}", item);
}

for mut values = vec![1,2,3];
for item in values.iter_mut() {
    println!("{}", item);
    *item = *item * 2;
}
```

```rust
let values = ["a".to_string(), "b".to_string(), "c".to_string()];
for item in values { // cause borrow checker error
    println!("{}", item);
}
println!("{}", values[1]);
```
for item in values 循环会将values 中的每个元素的所有权转移(move)给item. 一旦所有权被转移, values这个数组本身就不能再使用了。  
解决办法
```rust
// 如果你希望在循环后还能使用values, 可以通过借用(&)的方式访问它的元素。
let values = ["a".to_string(), "b".to_string(), "c".to_string()];
for item in &values {
    println!("{}", item);
}
println!("{}", values[1]);

// 使用 clone()
for item in values.clone() {
    println!("{}", item);
}
println!("{}", values[1]);

// 使用索引访问
for i in 0..values.len() {
    println!("{}", values[i]);
}
println!("{}", values[1]);

// 用 iter() 方法，返回一个不可变引用的迭代器
for item in values.iter() {
    println!("{}", item);
}
println!("{}", values[1]);
```
values.iter() 返回一个迭代器，迭代器中的每一项都是对数组元素的不可变引用(&String), 引用不会转移所有权。
```rust
// 借用数组元素的可变引用，允许修改元素内容
for item in values.iter_mut() {
    item.push_str(" modified");
}
println!("{:?}", values);
```
最后, 基础类型如i32实现了Copy,不会发生所有权转移。复杂类型如String,不实现Copy,会导致所有权转移。
## loop
The loop expression is an infinite loop by design. You can always interrupt a loop with a break.
```rust
let values = [1, 5, 6, 4, 5];
let mut iterator = values.iter();
let mut value;
let even = loop {
    value = iterator.next().unwrap();
    if value % 2 == 0 {
        break value;
    }
};
```
break can return the loop result, but only for loop.
## loop Label
With nested loops, you are tipically limited to breaking or continuing within the context of the current loop. With labels, Rust can continue or break in an outer loop.
```rust
let values = [[1,2,4,3],[5,6,7,8],[10,9,11,12]];
'newrow: for row in values {
    let mut prior = 0;
    for element in row {
        if prior > element {
            continue 'newrow;
        }
        prior = element;
    }
    println!("{:?} in order", row);
}

let mut outercount:i8 = 0;
'thewhile: while outercount < 10 {
    outercount += 1;
    let mut innercount: i8 = 1;
    println!();
    loop {
        print!("{} ", innercount);
        if innercount >= outercount {
            continue `thewhile;
        }
        innercount += 1;
    }
}
```

# Collections
- Array type: Array is a fixed-sized collection of values. The values of the array must be the same type.
- Vector type: Vector is an expandable collection of values that are the same type.
- HashMap type: A hash map is a lookup table that consists of (key,value) pairs.

## Arrays
The array type and size must be established at compile time.  
Within the square brackets stipulate the array type and number of elements.  
array_name[type; length]  [val1, val2,...]  [val; repeat]
```rust
let array_1:[i32;4] = [1,2,3,4];
let array_2 = [1,2,3,4];
let array_3 = [10; 5];

println!("")
```
Array values are stored in contiguous memory. If declared as a local variable, the values are placed on the stack. Arrays can also be boxed where the values are copied to the heap.
```rust
let array_1:[i32;1] = [1];
let array_2:[i32;2] = [1,2];
let array_3:[i32;3] = [1,2,3];
println!("a {:p}\nb {:p}\nc {:p}", &array_1, &array_2, &array_3);
```
## Accessing Array Values
Indexes are the usize type.
```rust
let t = ["aa","bb","cc"];
let u = t[1];
println!("{}", u);

let tt = ["aa".to_string(),"bb".to_string(),"cc".to_string()];
let uu = tt[1]; // ERROR
// let uu = &tt[1];
println!("{}", uu);
```
注意这两段代码的区别. 同样
```rust
>> let arr = [0,1,2,3,4,5];
>> arr[1..3] // 也会提示 [i32] doesnot implement the Copy trait.
>> &arr[1..3] // [1,2]
```
```rust
let array1 = [1,2,3,4];
let array2:[usize;4] = [1,2,3,4];
let array4 = [1,2,3];
>> array1 == array2 // no implementation for i32 == usize
>> array1 == array4 // no implementation for [i32;4] == [i32;3]
```
## Vectors
Vectors are dynamic arrays. Vector is unsized at compile time, itself cannot reside on the stack.  
A vector has three fields:
- The current size of the vector
- A pointer to the backing array on the heap
- The capacity of the backing array  

These fields are not directly available but accessed through functions, capacity(), len()..
```rust
let mut vec_1:Vec<i32> = Vec::new();
let mut vec_2 = Vec::new();
vec_2.push('a');
println!("{:?}", vec_2);
let vec_3 = vec![1,2,3];
vec_3.len()
vec_3.capacity()
let vec_4 = vec![[1,2],[3,4],[5,6]];
let var_1 = &vec_3[3];
let var_2 = &vec_4[1][1];

if let Some(var_3) = vec_3.get(3) {
    println!("{}", var_3);
} else {
    println!("Not found");
}

vec_3.push(5);
vec_3.push(10);
vec_3.pop();
println!("{:?}", vec_3);
vec_3.insert(1,4);

let vec_5 = Vec::with_capacity(4);
let vec_6 = vec![1,2,3];
vec_5.append(&mut vec_6); // len 3, capacity 4
vec_5.push(4);  // len 4, capacity 4
vec_5.reserve(6); // len 4, capacity 10
vec_5.push(8); // len 5, capacity 10
vec_5.shrink_to_fit(); // len 5, capacity 5
```

## HashMap
A hash map is a lookup table where entries consist of both a key and value. Keys can be virtually any type, including integers, floats, strings, structures, arrays, and even other hash maps.  
The HashMap<K,V> type is the implementation of a hash map in Rust. K is the key type, while V is the value type. The key type is flexible but must implement the Eq and Hash traits.  
The HashMap is used less frequently than arrays or vectors, is found in the std::collections::HashMap module.
```rust
let mut map_1:HashMap<char, f64> = HashMap::new();
let mut map_2:HashMap<String, String> = HashMap::new();
map_2.insert("English".to_string(), "Hello".to_string());
println!("{:?}", map_2);
map_2.remove(&"English".to_string()); // remove an entry

let famous_numbers = HashMap::from([
    ("Archimedes' Constant", 3.1415),
    ("Euler's Number", 2.7182),
    ("The Golden Ratio", 1.6180),
    ("Archimedes' Constant", 6.0221515*((10^23) as f64)),
])

let mut map:HashMap<&str,&str> = HashMap::new();
map.insert("English","Hello");
map.insert("Spanish","Hola");
let result = map.get("Spanish");
match result {
    Some(value) => println!("Found {}", value),
    None => println!("Not found")
}

map.insert("English", "Howdy"); // Update an entry
let res = map.insert("English","World");
match res {
    Some(previous) => println!("Previous: {}", previous),
    None => println!("New entry")
}

for (key,val) in map {
    println!("{} {}", key, val);
}
```

# Ownership
## Stack and Heap Memory
The stack is the private memory given each thread. When a function is run, a stack frame is added to the stack and consists of the function state, such as the local variables and parameters. When a function exists, the associated stack frame is removed from memory--the stack shrinks.  
The heap belongs to the application and is accessible from any thread. Various languages have different keywords, such as new and delete, for managing memory allocations on the heap. When allocated, a pointer is returned to the location on the heap where the memory resides.
## Shallow versus Deep Copy
Shallow copy is appropriate for most types, except pointers.  
When variableb is assigned variablea, a shallow copy is performed for this operation. This creates a dependency where both variables point to the same integer on the heap.  
When copying variablea to variableb, new memory is allocated for variableb's pointer.
## Move Semantics
```rust
let owner = String::from("automobile");
let newowner = owner; // Ownership of the String is then transferred to the new_owner variable
>> new_owner // automobile
>> owner // not found in this scope

fn buy_car(new_owner: String) {
    println!("{}", new_owner);
}
fn main() {
    let owner = String::from("automobile");
    buy_car(owner); // Ownership is transferred into the buy_car function.
    println!("{}", owner); // ERROR
}
```
## Borrow
Ownership can be borrowed instead of moved. 
```rust
fn borrow_car(borrower: &String) {
    println!("{}", borrower);
}
fn main() {
    let owner = String::from("automibile");
    borrow_car(&owner);
    println!("{}", owner); //works
}
```
```rust
let mut owner = String::from("automibile");
let borrower = &mut owner;
let borrower2 = &mut owner; // compile error
```
## Copy Semantics
Types with the Copy trait have copy semantics. When copied, a bitwise copy is performed.  
The Copy trait is a marker trait found in the std::marker module.  
```rust
let width = 10;
let height = width;
```
All scalar types have the copy trait. During the assignment, the width value is copied to height. width and height own separate values.
## Clone Trait
Implement the Clone trait when a deep copy is required. The String type implements the Clone trait.  
## Copy Trait
You can assign the Copy trait to types that support a shallow copy.
```rust
struct Transaction {
    debit: bool,
    amount：f32,
}
fn main() {
    let t1 = Transaction{debit: true, amount: 32.12};
    let t2 = t1; // moved
}
```
The Copy trait can be applied to structs if each field supports copy semantics.
```rust
#[derive(Debug,Copy,Clone)]
struct Transaction {
    debit: bool,
    amount: f32,
}
let t1 = Transaction{debit:true, amount:32.12};
let t2 = t1; // copied
```
If a String type field, description, is added, the struct no longer supports copy semantics. However, references support copy semantics.
```rust
#[derive(Copy,Clone)]
struct Transaction<'a> {
    description: &'a String,
    debit: bool,
    amount: f32,
}
...
let t2 = t1; // copied
```

# Lifetimes
Ownership, borrowing, lifetimes are the three pillars in the ownership model.  
The principal objective of lifetimes is to prevent dangling references. What is a dangling reference? A dangling reference occurs when a reference outlives a borrowed value. At that time, the reference points to invalid memory. 
```rust
fn main() {
    let ref1;
    {
        let num1 = 1;
        ref1 = &num1;
    }
    println!("{}", ref1); // num1 dropped here while still borrowed
}
```
The borrow checker relies on lifetimes to determine when a reference outlives the borrowed value.  
The interrelationship of various lifetimes can expose dangling references. A lifetime starts at the binding and continues until the variable is dropped. Lifetimes are named with an apostrophe and a variable name.  
By convention, lifetime names are alphabetic letters, starting with 'a. Subsequent lifetimes would be 'b,'c...  
## Function Headers and Lifetimes
Functions can create unsafe memory access. Functions that accept references as arguments and return values require special consideration.
## Lifetime Annotation
With lifetime annotations, you can formally name lifetimes.
```rust
fn do_something<'a>(ref1:&'a i32) -> &'a i32 {
    ref1
}
fn main() {
    let num1 = 1;
    let result = do_something(&num1);
    println!("{}", result);
}
```
## Static Lifetimes
A static lifetime, 'static, spans the entire application. string literals &str, have a static lifetime.

## runoob
Rust生命周期机制是与所有权机制同等重要的资源管理机制。 之所以引入这个概念是应对复杂类型系统中资源管理的问题。  
引用是对待复杂类型时必不可少的机制，毕竟复杂类型的数据不能被处理器轻易的复制和计算。  
但引用往往导致及其复杂的资源管理问题。  
悬垂引用的示例见第一段代码。 引用必须在值的生命周期内才有效。  
一直以来我们都在结构体中使用String而不是&str, 案例
```rust
fn longer(s1: &str, s2: &str) -> &str {
    if s2.len() > s1.len() {
        s2
    } else {
        s1
    }
}
```
这段代码通不过编译，返回值引用可能会返回过期的引用(返回值引用不知道它的生命周期是s1还是s2)。
```rust
&i32        // 常规引用
&'a i32     // 含有生命周期注释的应用
&'a mut i32 // 可变类型含有生命周期注释的引用
```
```rust
fn longer<'a>(s1: &'a str, s2: &'a str) -> &'a str {
    if s2.len() > s1.len() {
        s2
    } else {
        s1
    }
}

fn main() {
    let r;
    let s1 = "rust";
    let s2 = "python";
    r = longer(s1, s2);
    println!("{} is longer", r);
}
```

# References
References, &T, are primitives and the preferred pointer type in Rust. Raw pointers are outside the scope of the borrow checker. 
```rust
let val_a = 10;
let ref_a: &i32 = &val_a;
let ref_b = ref_a;
```
The lifetime of the owner must outlive the borrower(reference).
## Deferencing
The asterisk operator(*) provides access to the referenced value.
```rust
let ref_a = &10;
let ref_b = &20;
// let result = *ref_a + *ref_b;
let result = ref_a + ref_b; // Implicit deferencing
println!("{:p}", ref_a); // displays memory location

let ref_c = &10;
>> ref_a == ref_c // true
>> use std::ptr;
>> ptr::eq(ref_a, ref_c) // false
```
## Mutability
References default to being immutable. add mut keyword to declare a mutable value.  
- Reference itself: the reference can be updated, the referenced value remains immutable.
- Referenced value: referenced value can change but not the reference.

```rust
let mut val_a = 10;
val_a = 15;

let mut val_b = 20;
let mut ref_a:&i32 = &val_a;
ref_a = &val_b;
// *ref_a = 30;  // ERROR, ref_a is a '&' reference, so the data it refers to cannot be written
let mut ref_a: &mut i32 = &mut val_a;
ref_a = &mut val_b;
*ref_a = 30;
```

# Functions
The caller is the function that invokes another function, while the callee is the function being called. The state of the caller is preserved on the stack, such as the locals and return instruction, while the callee executes.  
Most functions that appear variadic in Rust are actually macros, such as the prinln! macro.
```rust
fn swap_values(mut value1:i32, mut value2:i32) {
    let temp = value1;
    value1 = value2;
    value2 = temp;
    println!("{} {}", value1, value2);
}

fn main() {
    let (num1, num2) = (5, 10);
    swap_values(num1, num2);
}
```
The parameters are mutable only within the context of the function. The num1 and num2 are not changed.  
When passed by reference, you can dereference(*) the function parameter to access the referenced value. If it is a mutable reference, you can also change the referenced value.
## return and implicit return
```rust
fn min_max(data:Vec<i32>) -> (i32, i32) {
    let mn = *data.iter().min().unwrap();
    let mx = *data.iter().max().unwrap();
    (mn, mx)
}
```
## return a reference to a local value
```rust
fn lift_value() -> &i32 {
    let value = 5;
    &value // ERROR
}
```

# Error Handling
Rust standardizes around the Result and Option types.  
Proactive error handling is preferred to reactive error handling for several reasons.

In Rust, errors can be classified into two major categories as shown in the table below.  
| Name & Description | Usage |
| --- | --- |
| Recoverable, Errors which can be handled | Result enum |
| UnRecoverable, Errors which cannot be handled | panic marco |

Unlike other programming languages, Rust does not have exceptions. It returns an enum Result<T,E> for recoverable errors, while it calls the panic macro if the program encounters an unrecoverable error. The panic macro causes the program to exit abruptly.
## Panic Macro and Unrecoverable Errors
panic! macro allows a program to terminate immediately and provide feedback to the caller of the program. It should be used when a program reaches an unrecoverable state.
```rust
fn main() {
//    panic!("Hello");
    println!("End of main");
    let a = [10,20,30];
    a[10];
}
```
## Result Enum and Recoverable Errors
```rust
enum Result<T,E> {
    OK(T),
    Err(E)
}
```
```rust
use std::fs::File;

fn main() {
    let f = File::open("main.jpg");
    match f {
        Ok(f) => {
            println!("file found {:?}", f);
        },
        Err(e) => {
            println!("file not found \n{:?}", e);
        }
    }
    println!("end of main");

    let result = is_even(10).unwrap();
    println!("result is {}",result);
}

fn is_even(no:i32) -> Result<bool,String> {
    if no % 2 == 0 {
        return Ok(true);
    } else {
        return Err("NOT_AN_EVEN".to_string());
    }
}
```

# Structures
Structures are custom types that consist of fields and functions.  
A struct can aggregate data into a cohesive unit that is easier to manage than the individual components.  
You can create a struct value using the let, const or static keyword. Similar to the primitives, the rules of scope, shadowing, ownership, and lifetimes apply to struct.  
Structs and tuples are close relatives. Both are custom types with heterogeneous fields. Structs, however, are named types, while tuples are unnamed. In addition, structs also have named fields, while fields within tuples are numbered. These differences mean structs are generally easier to use.
```rust
struct Structname {
    field1: type,
    field2: type,
    field3: type
}
```
By default, the order of fields in the struct is inconsequential. Use repr() attribute to control the order of fields.
```rust
struct RGB  {
    red: u8,
    green: u8,
    blue: u8,
}
```
```rust
RGB{red:50, green:50, blue:50};
```
```rust
#[derive(Debug)]
struct RGB {
    red: u8,
    green: u8,
    blue: u8,
}

fn main() {
    let dark_gray = RGB{red:50, green:50, blue:50};
    let orange = RGB{red:255, green:165, blue:0};
//    println!("{:?} {:?}", dark_gray, orange);
    dbg!(&dark_gray);
    dbg!(&orange);

    // let other_color = CMYK(key:100, ..school_bus_yellow);
}

fn new_struct(red:u8, green:u8, blue:u8) -> RGB {
    // RGB(red:red, green:green, blue:blue) // longhand
    RGB(red, green, blue) // shorthand
    // RDB(red:120, green, blue) // both
}
```
## Move Semantics
Structs support move semantics.
```rust
#[derive(Debug,Copy,Clone)]
struct RGB {
    red: u8,
    green: u8,
    blue: u8
}
```
with the Copy trait added, the preceding code let mut light_gray = dark_gray; will not dark_gray lose ownership.
## Methods
```rust
#[derive(Debug)]
struct RGB {
    red: u8,
    green: u8,
    blue: u8,
}
impl RGB {
    fn is_gray(self: &Self) -> bool {
        (self.red==self.blue)&&(self.blue==self.green)
    }
}
```
methods are implemented within one or more impl blocks, which binds a method to a particular struct.  
The first paramter of the method is the &Self parameter, which is a reference to the current instance.  
&self is the short syntax for self:&Self
```rust
impl RGB {
    fn invert(&mut self) {
        self.red = 255 - self.red;
        self.green = 255 - self.green;
        self.blue = 255 - self.blue;
    }
}

let mut color = RGB{red:150, green:50, blue:75};
color.invert();
dbg!(&color);
```
## struct vs enum
描述一个具有多个属性的实体，使用struct.
```rust
struct User {
    username: String,
    email: String,
    age: u8,
}
```
表示多种状态或变体(多态性), 使用enum
```rust
enum Shape {
    Circle(f64),
    Rectangle(f64, f64),
}
```

# Generics
Many of the types in the standard library, such as Result<T,E>, Option<T>, Vec<T>, and HashMap<K,V> are generic. 
## Generic Functions
```rust
fn functionname<T>(param:T) -> T {
    let variable: T;
}
```
```rust
fn main() {
    let tuple1 = (10, 20);
    let tuple2 = ("ten".to_string(), "twenty".to_string());
    let tuple3 = (10.0, 20.0);
    // let result = swap(tuple1);
    // let result = swap_string(tuple2);
    // let result = swap_float(tuple3);
    println!("{:?}", swap(tuple1));
    println!("{:?}", swap(tuple2));
    println!("{:?}", swap(tuple3));
}
// fn swap<T>(tuple: (T,T)) -> (T,T) {
//     (tuple.1, tuple.0)
// }
fn swap<T,U>(tuple: (T,U)) -> (U,T) {
    (tuple.1, tuple.0)
}
```

# Patterns

# Closures
A closure is an anonymous function that can "close over" variables from an outer function.  
Like any function, closures execute code, have parameters, and return values.  
Advantages:  
- Closures are convenient when there is a single reference to the function.
- Closures are ideal as first-class citizens. You can treat closures as function parameters, return values, or even assign to a variable.
- Closures are often defined close to where the function is used, which makes the code more maintanable.  

Closures implement the Fn, FnMut, or FnOnce traits.  
Finally, closures are not nested functions. Nested functions do not have the capability to close over variables in the outer function. In addition, nested functions are named, while closures are anonymous.
```rust
fn main() {
    let hello = || println!("Hello, world!");
    hello();
}
```
Closures start with the || syntax. Next is the implementation of the closure.  
You can also call the closure directly.
```rust
(|| println!("Hello, world!"))();
```
```rust
let cubed = |number| number*number*number;
```
## Closed Over
Closures can close over a free variable. The captured variable is then available within the closure. Most often, captured variables are borrowed from the outer function.
```rust
fn main() {
    let value=5;
    let cubed=||value*value*value; // the closure captures the free variable.
    let result=cubed();
    println!("{}", result);
}
```
## Closures as Function Arguments
```rust
fn do_closure(run: impl Fn()) {
    run();
}

fn main() {
    let display = || println!("message");
    do_closure(display);
}
```
```rust
enum Calculation {
    Cubed,
    Quad
}

fn get_result(run: impl Fn(i32)->i32, value:i32) -> i32 {
    run(value)
} 

fn main() {
    let cubed = |value:i32| value*value*value;
    let quad = |value:i32| value*value*value*value;
    let calculation_type = Calculation::Cubed;
    let result = match calculation_type {
        Calculation::Cubed => get_result(cubed, 5),
        Calculation::Quad => get_result(quad, 5),
    };
    println!("{}", result);
}
```
## Closures as Function Return Values
Returning a closure from a function can be done with the impl keyword.
```rust
fn get_closure() -> impl Fn(i32)->i32 {
    |number| number*number*number
}

fn main() {
    let cubed = get_closure();
    let result = cubed(5);
    println!("{}", result);
}
```

# Traits
Traits are implemented by concrete types. Traits create relationships between types. For example, the GasCar, ElectricCar, and HybridCar types could implement a Car trait. They share a common interface and behavior, such as start,brake,accelerate and turn functions.  
Types can implement more than one trait.   
Fundamentally, traits are contracts. Types that expose a trait must fully implement it.
## Define a trait
```
trait TraitName {
    fn method_one(&self, [arguments:type]) -> return_type;
    fn method_two(&mut self, [arguments:type]) -> return_type;
    ...
}
```
## Implement a trait
```
impl TraitName for TypeName {
    fn method_one(&self, [arguments:type]) -> return_type {
        // implementation
    }
    fn method_two(&mut self, [arguments:type]) -> return_type {
        // implementation
    }
    ...
}
```
## The derive keyword
'derive' can be used in a struct or enum definition.
```rust
[derive(Copy,Clone)]
```
use the derive keyword to implement trait Copy and Clone from the Rust standard library.

# Threads
Each path of execution in a process is known as a thread. The threads share the single processor. This is called concurrency. Operating systems schedule threads onto the processor mainly in a round-robin fashion.  
Multicore devices are now prevalent. We have true parallelization, where processes are decomposed into threads that run in parallel across multiple processors.  
A server application with a thread for each client connection is a perfect example of parallel programming.  
When thread a spawns thread b, logically thread a is the parent thread. However, technically there is no relationship between the two threads.  
```rust
use std::thread;

fn main() { // primary thread 
    thread::spawn(|| println!("Hello, world!")); //another thread 
    println!("In Main");
}
```
Worst of all, running the program several times may result in different outcomes. The hello message may or may not be displayed.  
When the primary thread exits, the program is terminated. This includes terminating other threads still running.  
The fork/join model prevents race conditions that can occur when parallel threads are racing for the exit. The spawn function implements the fork/join model. The spawn function returns a JoinHandle. The JoinHandle::join method blocks the current thread until the associated thread exits.  
The new thread could outlive the parent thread, ownership of the captured data must be moved into the thread with the move keyword.

# Macros
The println! macro is the most well-known macro in the Rust universe. It provides the capability of a variadic function. A close second in popularity is the derive attribute. The derive attribute for the Clone and Copy traits, is actually a macro that implements a default behavior of those trait.  
Fundamentally, a macro is code that generates code -- otherwise known as metaprogramming. Macros are evaluated at compile time.  
For many languages, macros are an add-on for developers, but not with Rust. Macros play an important role even in the core language.  
Macros have the following features that are distinct from functions:  
- Macros support variadic parameters.
- Macros are expanded at compile time.
- Macro variables are untyped.
- Macros have a different error-handling model.
- Macro logistics can be different, such as where a macro can reside.

There are two flavors of macros: declarative and procedual macros. Macros: more versatile, more complex, less transparent or readable. If a task can be accomplished adequately with a function, you should choose a function!