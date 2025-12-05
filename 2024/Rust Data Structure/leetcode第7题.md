## 第7题 反转整数
输入32位有符号整型，输出数位反转后的数，如果超出32位整型范围[-2^31, 2^31-1], 则返回0.  
123  ->  321  
-123  ->  -321  
120  ->  21

## Python版本
```python
def reverse(x:int) -> int:
	if x < 0:
		res = -int(str(-x)[::-1])
	else:
		res = int(str(x)[::-1])
	return res if -2**31 <= res <= 2**31-1 else 0
```
Runtime:  24ms  
Memory:  12.74MB  

## Rust版本
```rust
pub fn reverse(x: i32) -> i32 {
    let mut res: i32 = 0;
    let mut cur: i32 = x;
    while cur != 0 {
        match res.checked_mul(10) {
            None => return 0,
            Some(tmp) => match tmp.checked_add(cur % 10) {
                None => 0,
                Some(fine) => {
                    res = fine;
                }
            }
        }
        cur /= 10;
    }
    res
}
```
Runtime:  0ms  
Memory:  2.12MB
