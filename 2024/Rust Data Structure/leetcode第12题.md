## 第12题 整数转罗马数字
| Symbol | Value |
| --- | --- |
| I | 1 |
| V | 5 |
| X | 10 |
| L | 50 |
| C | 100 |
| D | 500 |
| M | 1000 |

罗马数字从左写到右, 此外: 4 -> IV, 9 -> IX, 40 -> XL, 90 -> XC, 400 -> CD, 900 -> CM  
给定 1 <= num <= 3999, 给出罗马数字  
3  ->  III  
58  ->  LVIII  
1994  ->  MCMXCIV

### Python版本
```python
def intToRoman(num:int) -> str:
    q = ['','M','MM','MMM']
    b = ['','C','CC','CCC','CD','D','DC','DCC','DCCC','CM']
    s = ['','X','XX','XXX','XL','L','LX','LXX','LXXX','XC']
    g = ['','I','II','III','IV','V','VI','VII','VIII','IX']
    return q[num//1000] + b[num%1000//100] + s[num%100//10] + g[num%10]
```
Runtime:  48ms  
Memory:  16.49MB

### Rust版本
```rust
pub fn int_to_roman(num: i32) -> String {
    let num = num as usize;
    let q = ["", "M", "MM", "MMM"];
    let b = ["", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"];
    let s = ["", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"];
    let g = ["", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"];
    format!("{}{}{}{}", q[num/1000], b[num%1000/100], s[num%100/10], g[num%10])
}
```
Runtime:  3ms  
Memory:  2.04MB