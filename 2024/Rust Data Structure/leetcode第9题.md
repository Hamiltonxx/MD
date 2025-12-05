## 第9题 回文数
判断整数x是否是回文数.  
121  ->  true  
-121  ->  false  
10  ->  false  

### Python版本
```python
def isPalindrome(x: int) -> bool:
    return str(x)==str(x)[::-1]
```
Runtime:  60ms  
Memory:  12.78MB  

### Rust版本三种解法
```rust
pub fn is_palindrome(x: i32) -> bool {
    let mut temp = x;
    let mut reversed = 0;
    let mut remainder;
    while temp > 0 {
        remainder = temp % 10;
        reversed = reversed * 10 + remainder;
        temp /= 10;
    }
    x == reversed
}
```
Runtime:  3ms  
Memory:  2.01MB  

```rust
pub fn is_palindrome(x: i32) -> bool {
    let chars: Vec<char> = x.to_string().chars().collect();
    let (mut i, mut j) = (0, chars.len().saturating_sub(1));
    while i < j && chars[i]==chars[j] {
        i += 1;
        j = j.saturating_sub(1);
    }
    i >= j
}
```
Runtime:  0ms  
Memory:  2.20MB  

```rust
pub fn is_palindrome(x: i32) -> bool {
    x.to_string().chars().rev().eq(x.to_string().chars())
}
```
Runtime:  0ms  
Memory: 2.10MB