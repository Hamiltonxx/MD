## split string into two variables
```rust
let my_string = "Hello, World!";
if let Some((first,second)) = my_string.split_once(",") {
    println!("First part: {}", first);
    println!("Second part: {}", second);
}
```
Splits the string at the first occurrence of the specified delimiter. The `if let` checks if the result is `Some`, which means that the delimiter was found.

## create an array
```rust
let my_array = [1,2,3,4,5];
let my_array: [i32; 5] = [1,2,3,4,5];
let first_element = my_array[0];
let third_element = my_array[2];
```
Rust arrays have a fixed size.

## concatenate strings
```rust
let str1 = String::from("hello");
let str2 = String::from("world");
let str3 = str1 + &str2;
println!("{}", str3);
let str5 = String::from("hello");
let str6 = String::from("world");
let str4 = format!("{} {}", str5, str6);
// let str4 = format!("{} {}", str1, str2);
println!("{}", str4);
let mut str7 = String::from("hello");
let str8 = "world";
str7.push_str(str8);
println!("{}", str7);
let mut str9 = String::from("hello");
let str10 = String::from(" ");
let str11 = String::from("world");
let strings = vec![str10,str11];
str9.extend(strings);
println!("{}", str9);
let str12 = "hello";
let str13 = " ";
let str14 = "world";
let str15 = str12.to_owned() + str13 + str14;
println!("{}", str15);
```
The `+` operator requires a reference to a String, not an owned String.  
Append a &str to an existing String object using push_str.  
Extend a vector of String objects.  
Concatenate multiple &str objects into a single String.  

## join integer with string
```rust
let number = 42;
let text = "The answer is";
let result = format!("{} {}", text, number);
println!("{}", result);	
```

## create and print tuples
```rust
let my_tuple = (1, "hello", 3.14);
println!("The first element: {}", my_tuple.0);
println!("The third element: {}", my_tuple.2);
let (a,b,c) = my_tuple;
println!("Desctucted tuple by a={}, b={}, c={}", a,b,c);
println!("{:?}", my_tuple);
```
Use `:?` to print the whole tuple, which requires the type to implement the `std::fmt::Debug` trait. All the types within the tuple here implement the trait. 

## iterate over an array
```rust
let my_array = [1,2,3,4,5];
for item in my_array.iter() {
    println!("{}", item);
}
for (index,item) in my_array.iter().enumerate() {
    println!("{}: {}", index, item);
}
```

## iterate over an vector
```rust
let v = vec![1,2,3,4,5];
for i in &v {
    println!("{}", i);
}
for i in v.iter() {
    println!("{}", i);
}
let mut v = vec![1,2,3,4,5];
for i in &mut v {
    *i *= 2;
}	
println!("{:?}", v);
```

## if let, while let
```rust
let x = Some(10);
if let Some(y) = x {
    println!("The value of y is: {}", y);
} else {
    println!("x is None");
}

let stdin = io::stdin();
let mut lines = stdin.lock().lines();
while let Some(line) = lines.next() {
    let line = line.unwrap();
    if line.is_empty() {
        break;
    }
    println!("{}", line);
}
```

## loop
```rust
let numbers = vec![1,3,5,4,7,9];
let mut first_even = None;
for num in numbers {
    if num % 2 == 0 {
        first_even = Some(num);
        break;
    }
}
match first_even {
    Some(num) => println!("The first even number is {}", num),
    None => println!("There are no even numbers"),
}
```
```rust
'outer: for i in 1..=3 {
    for j in 1..=3 {
        if i == 2 && j == 2 {
            break 'outer;
        }
        println!("i = {}, j = {}", i, j);
    }
}

// find the smallest even number greater than 10 in a matrix
let matrix = [[1,2,3],[4,5,6],[7,8,9],[10,11,12]];
let mut smallest_even = i32::max_value();
'outer: for i in 0..matrix.len() {
    for j in 0..matrix[i].len() {
        if matrix[i][j] > 10 && matrix[i][j] % 2 == 0 {
            smallest_even = matrix[i][j];
            break 'outer;
        }
    }
}
println!("The smallest even number greater than 10 is {}", smallest_even);
```
```rust
let mut i = 0;
let result = loop {
    if i == 10 {
        break i * 2;
    }
    i += 1;
};
println!("The result is {}", result);
```

## get current date and time
```
use std::time::SystemTime;
use chrono::{Local, DateTime};

let now = SystemTime::now();
println!("Current date and time: {:?}", now);	
let now: DateTime<Local> = Local::now();
println!("Current date and time: {:?}", now);
println!("{:?}", now.format("%Y-%m-%d %H:%M:%S").to_string());
```

## initialize vector
```rust
let v = vec![0; 5];
println!("{:?}", v);
let v = vec![1,2,3];
println!("{:?}", v);
let v = vec![i*i; 5];
println!("{:?}", v);
```

## add or remove characters from string
```rust
let mut a:String = "Rat".to_string();
println!("{}", a);
a.remove(0);
println!("{}", a);
a.insert(0, 'C');
println!("{}", a);
a.pop();
println!("{}", a);
a.push('r');
println!("{}", a);
a.insert(3,'s');
println!("{}", a);
```

## reverse a string
```rust
let test = "Hello World";
let t: String = test.chars().rev().collect();
println!("{}", t);
for word in test.split_whitespace().rev() {
    print!("{}", word);
}
println!("");
let s = "abć→"; // Unicode characters
for c in s.chars().rev() {
    print!("{}", c);
}
```

## print
```rust
println!("{:#010}", 5i8);
println!("{:#018}", 5i16);
println!("{:#032}", 5i32);
println!("{:0>8}", "110");
println!("{:-^30}", "SO is AWESOME");
println!("love: {:♥<5}", "#");
println!("love: {:♥>5}", "#");
println!("love: {:♥^5}", "#");
println!("love: {:♥<3}", "");
println!("love: {:♥<1$}", "", 5);
println!("{:#06x}", 0x0001u16);
println!("{}, `{name:.*}` has 3 fractional digits", "Hello", 3, name=1234.56);
println!("Hello {:^15}!", format!("{:?}", Some("hi")));
println!("Hello {:1$}!", "x", 5);
println!("Hello {1:0$}!", 5, "x");
println!("Hello {:width$}!", "x", width=5);
```
```rust
println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");
println!("{subject} {verb} {object}", object="The lazy dog", subject="The quick brown fox", verb="jumps over");
println!("{:indent$}{}", "Hello", "World", indent=10);
```
```rust
println!("Value of {0} is {1:.5}", "x", 0.01);
println!("Value of {1} is {2:.0$}", 5, "x", 0.01);
println!("Value of {} is {:.*}", "x", 5, 0.01);
println!("Value of {} is {2:.*}", "x", 5, 0.01);
```

## string match
```rust
let s = String::from("Canada");
match s.as_str() {
    "Japan" => {
        println!("Match");
    },
    _ => {
        println!("UnMatch");
    }
}

let test = String::from("canada");
match &*test {
    "japan" => println!("0"),
    "canada" => println!("1"),
    _ => println!("-1"),
}
match &test as &str {
    "japan" => println!("0"),
    "canada" => println!("1"),
    _ => println!("-1"),
}
match test.as_ref() {
    "japan" => println!("0"),
    "canada" => println!("1"),
    _ => println!("-1"),
}
match &test[..] {
    "japan" => println!("0"),
    "canada" => println!("1"),
    _ => println!("-1"),
}
```

## extract characters from string
```rust
let s = String::from("Hamilton");
println!("{}", s[s.len()-3..].to_string());
let s = "Hamilton";
println!("{}", s[s.len()-3..].to_string());
let s:&str = "Hamilton";
println!("{}", s[s.len()-3..].to_string());
```
