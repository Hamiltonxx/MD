## basic data type
基本数据类型
```rust
fn main () {
    let a: i8 = -2;
    let b: f32 = 2.34;
    let c: bool = true;
    let d: char = 'a';

    let x: (i32, f64, u8) = (200, 5.32, 1);
    let xi32 = x.0;
    let xf64 = x.1;
    let xu8 = x.2;

    println!("{a} {b} {c} {d} {x:?} {xi32} {xf64} {xu8}");

    let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
    let first_month = months[0];
    let halfyear = &months[..6];
    let mut monthsv = Vec::new();
    for month in months {
        monthsv.push(month);
    }

    println!("{months:?}\n{first_month}\n{halfyear:?}\n{monthsv:?}");
}
```

## vector methods
1. Vec::new() Creates an empty vector
2. vec![value1, value2, ...] The values can be of any type.
3. push(element) append an element to the end of the vector
4. pop() removes and returns the last element of the vector
5. insert(index, element) inserts an element at the specified index
6. remove(index) Removes and returns the element at the specified index
7. get(index) returns an Option that contains a reference to the element or None if the index is out of bounds.
8. get_mut(index) retrives an mutable reference, Option, allows you to modify the element
9. iter() returns an iterator over the elements
10. iter_mut() mutable iterator, allows you to iterate and modify the elements.
11. sort()
12. binary_search(&value) returns either Ok(index) or Err(index), The vector must be sorted beforehand.
```rust
fn main() {
    let mut numbers : Vec<i32> = Vec::new();
    numbers.push(10);
    numbers.push(20);
    numbers.push(30);
    println!("First element: {}", numbers[0]);
    numbers[1] = 25;
    for number in &numbers {
        println!("Number: {}", number);
    }
    println!("poped number: {}", numbers.pop().unwrap());
    numbers.insert(1,15);
    println!("Removed number: {}", numbers.remove(0));
    println!("{:?}", numbers);

    if let Some(index) = numbers.iter().position(|&x| x == 30) {
        println!("Found 30 at index: {}", index);
    } else {
        println!("30 Not Found");
    }

    numbers.sort();
    println!("sorted vector: {:?}", numbers);
}
```

## anagram
判断两个字符串是否只是字母的不同排列
```rust
fn anagram(s1: &str, s2: &str) -> bool {
    if s1.len() != s2.len() { return false; }

    let mut c1 = [0; 26];
    let mut c2 = [0; 26];
    for c in s1.chars(){
        let pos = (c as usize) - 97; // 97为a的ASCII值
        c1[pos] += 1;
    }
    for c in s2.chars() {
        let pos = (c as usize) - 97;
        c2[pos] += 1;
    }
    for (elem1, elem2) in c1.iter().zip(c2.iter()) {
        if elem1 != elem2 {
            return false;
        }
    }
    true
}

#[test]
fn test_anagram() {
    assert!(anagram("hello", "oellh"));
}
#[test]
fn test_anagram2() {
    assert!(anagram("datastructuredatastructuredatastructuredatastructuredatastructuredatastructure", "datastructuredatastructuredatastructuredatastructuredatastructuredatastructure"));
}
```

## stack
模拟stack
```rust
// 抽象数据类型的实现多选择创建新的结构体struct
#[derive(Debug)]
struct Stack<T> {
    top: usize,
    data: Vec<T>,
}

impl<T> Stack<T> {
    fn new() -> Self {
        Stack {
            top: 0,
            data: Vec::new()
        }
    }

    fn push(&mut self, val:T) {
        self.data.push(val);
        self.top += 1;
    }

    fn pop(&mut self) -> Option<T> {
        if self.top == 0 { return None; }
        self.top -= 1;
        self.data.pop()
    }

    fn peek(&self) -> Option<&T> {
        if self.top == 0 { return None; }
        self.data.get(self.top - 1)
    }

    fn is_empty(&self) -> bool {
        0 == self.top
    }

    fn size(&self) -> usize {
        self.top 
    }
}

fn main() {
    let mut s = Stack::new();
    s.push(1); s.push(2); s.push(4);
    println!("top {:?}, size {}", s.peek().unwrap(), s.size());
    println!("top {:?}, size {}", s.pop().unwrap(), s.size());
    println!("is_empty:{}, stack:{:?}", s.is_empty(), s);
}
```

## bracket matching
括号匹配
```rust
fn is_balanced(expression: &str) -> bool {
    let mut stack: Vec<char> = Vec::new();

    for c in expression.chars() {
        match c {
            '(' | '[' | '{' => stack.push(c),
            ')' => {
                if stack.pop() != Some('(') {
                    return false;
                }
            }
            ']' => {
                if stack.pop() != Some('[') {
                    return false;
                }
            }
            '}' => {
                if stack.pop() != Some('{') {
                    return false;
                }
            }
            _ => {} // Ignore
        }
    }

    stack.is_empty()
}

fn main() {
    let expression1 = "((2 + 3) * [4 - 1])";
    let expression2 = "{[(1 + 2) * (3 - 4)]}";
    let expression3 = "[(2 + 2) - 1}";
    let expression4 = "{[2 + 2) - 1]";

    println!("Expression 1 is balanced: {}", is_balanced(expression1));
    println!("Expression 2 is balanced: {}", is_balanced(expression2));
    println!("Expression 3 is balanced: {}", is_balanced(expression3));
    println!("Expression 4 is balanced: {}", is_balanced(expression4));
}
```

## binary decimal transform
二进制与十进制互换, 用format!("{:b}") 和 u32::from_str_radix(s,2) 来做进制转换
```rust
fn main() {
    let decimal_number: u32 = 42;
    let binary_number: String = format!("{:b}", decimal_number);
    println!("Decimal: {}", decimal_number);
    println!("Binary: {}", binary_number);

    let binary_string = "101010";
    let decimal_number: u32 = u32::from_str_radix(binary_string, 2).unwrap();

    println!("Binary: {}", binary_string);
    println!("Decimal: {}", decimal_number);
}
```

## infix to postfix
前中后缀  
根据运算符在操作数之前、之间或之后来定义  
前缀: + 2 3;  中缀: 2 + 3;  后缀: 2 3 +  
在计算机中，常用后缀表达式来进行算术运算  
中缀表达式用括号来确定优先级，前后缀不需要  
```rust
fn is_operator(c: char) -> bool {
    c == '+' || c == '-' || c == '*' || c == '/'
}

fn precedence(c: char) -> i32 {
    match c {
        '+' | '-' => 1,
        '*' | '/' => 2,
        _ => 0,
    }
}

fn infix_to_postfix(expression: &str) -> String {
    let mut postfix: String = String::new();
    let mut stack: Vec<char> = Vec::new();

    for c in expression.chars() {
        if c.is_whitespace() {
            continue;
        }
        if c.is_digit(10) {
            postfix.push(c);
        } else if is_operator(c) {
            while let Some(top) = stack.last() {
                if is_operator(*top) && precedence(*top) >= precedence(c) {
                    postfix.push(stack.pop().unwrap());
                } else {
                    break;
                }
            }
            stack.push(c);
        } else if c == '(' {
            stack.push(c);
        } else if c == ')' {
            while let Some(top) = stack.pop() {
                if top == '(' {
                    break;
                } else {
                    postfix.push(top);
                }
            }
        }
    }

    while let Some(top) = stack.pop() {
        postfix.push(top);
    }

    postfix
}

fn main() {
    // let infix_expression = "3 + 4 * 2 / ( 1 - 5 ) ^ 2 ^ 3";
    let infix_expression = "(2 + 3) * 4 - 5";
    let postfix_expression = infix_to_postfix(infix_expression);
    println!("{}", postfix_expression);
}
```
