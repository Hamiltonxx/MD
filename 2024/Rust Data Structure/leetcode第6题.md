## 第6题 ZigZag转换(来回读)
PAYPALISHIRING, 3  ->  PAHNAPLSIIGYIR  
```shell
P   A   H   N
A P L S I I G
Y   I   R 
```
```python
def convert(s:str, numRows:int) -> str:
    if numRows == 1:
        return s
    t = [""] * numRows
    idx = 0
    inc = 1
    for x in s:
        t[idx] += x
        if idx == 0:
            inc = 1
        elif idx == numRows - 1:
            inc = -1
        idx += inc
    return "".join(t)
```
Runtime:  39ms  
Memory:  16.58MB  

```rust
pub fn convert(s: String, rs: i32) -> String {
    if rs == 1 {
        return s;
    }

    let mut t = vec![String::new(); rs as usize];
    let mut idx: i32 = 0;
    let mut inc: i32 = 1;

    for x in s.chars() {
        t[idx as usize].push(x);
        if idx == 0 {
            inc = 1;
        }else if idx == rs - 1 {
            inc = -1;
        }
        idx += inc;
    }

    t.join("")
}
```
Runtime:  6ms  
Memory:  2.27MB  

