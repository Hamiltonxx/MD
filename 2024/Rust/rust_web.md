# enums
If we tried to store strings and integers in the same array, we would have a problem. How would we define the type?  
```rust
enum SomeValue {
    StringValue(String),
    IntValue(i32)
}
let multi_array: [SomeValue; 4] = [
    SomeValue::StringValue(String::from("one")),
    SomeValue::IntValue(2),
    SomeValue::StringValue(String::from("three")),
    SomeValue::IntValue(4)
];
for i in multi_array {
    match i {
        SomeValue::StringValue(data) => {
            println!("The string is: {}", data);
        },
        SomeValue::IntValue(data) => {
            println!("The int is {}", data);
        }
    }
}
```
Using enums to wrap data and match statements to handle them can be applied to HashMaps and vectors.  

# Metaprogramming
```rust
#[derive(Debug,Copy,Clone)]
struct Coordinate <T,U> {
    x: T,
    y: U
}

fn main() {
    let one = Coordinate{x:50, y:50};
    let two = Coordinate{x:500, y:500};
    let three = Coordinate{x:5.6, y:5.6};
    let four = Coordinate{x:5.6, y:500};

    dbg!(one);
    dbg!(two);
    dbg!(three);
    dbg!(four);
}
```
Macros enable us to write code that writes code at compilation time.  
Defining our own macros is a blend of defining a function and using a lifetime notation within a match statement in the function.
```rust
macro_rules! capitalize {
    ($a: expr) => {
        let mut v: Vec<char> = $a.chars().collect();
        v[0] = v[0].to_uppercase().nth(0).unwrap();
        $a = v.into_iter().collect();
    }
}
fn main() {
    let mut x = String::from("test");
    capitalize!(x);
    println!("{}",x);
}
```

# A command-line todo program
Structuring code across multiple files and directories is an important aspect of building software.  
Our program will be structured in a scalable way where we build and manage our own modules. which will be imported into other areas of the program and utilized.
## Cargo
When compiling Rust code, Rust files are compiled into object files using optimization techniques. These object files are then linked with required libraries, and a binary output file is produced. 
```rust
use clap::Parser;
#[derive(Parser)]
#[command(version, about)]
struct Args {
    #[arg(short, long)]
    first_name: String,
    #[arg(short, long)]
    last_name: String,
    #[arg(short, long, default_value_t = 1)]
    age: u8,
}
fn main() {
    let args = Args::parse();
    dbg!(args);
}
```
The reason why crates such as clap are useful is that they are essentially self-documenting.
## Structuring code through Nanoservices
The mod.rs enables us to declare files in the module. 

## Data Access Layer
Handle all the interactions between logic and persistent storage.

# Async Rust
The average web framework utilizes async code.  
Waiting for the threads to complete before moving on is called joining.
## async await
Instead of simply spawning off threads, we create futures and then manipulate them as and when needed.  
In computer science, a future is an unprocessed computation. A future is a way of expressing a value that is not yet ready. For instance, we have several network connections. We could have an individual thread for each network connection. This is better than sequentially processing all connections, as a slow network connection would prevent other faster connections being processed. However, spinning up threads for every network connection is not free as we must allocate memory for the thread. Instead, we can have a future for each network connection. These network connections can be processed by a thread from a thread pool when the future is ready.  
Futures can also be referred to as promises, delays, or deferred.  
Future does not execute until we apply an executor using await.  
The await syntax enables the task executor to switch to other async tasks.
## async task queue

