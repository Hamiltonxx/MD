## 第13题 罗马数转整数
| Symbol | Value |
| --- | --- |
| I | 1 |
| V | 5 |
| X | 10 |
| L | 50 |
| C | 100 |
| D | 500 |
| M | 1000 |

III  ->  3  
LVIII  ->  58  
MCMXCIV  ->  1994   

### Rust版本
```rust
pub fn roman_to_int(s:String) -> i32 {
    let s_translated = s
        .replace("IV", "IIII")
        .replace("IX", "VIIII")
        .replace("XL", "XXXX")
        .replace("XC", "LXXXX")
        .replace("CD", "CCCC")
        .replace("CM", "DCCCC");

    s_translated.chars().map(|c| {
        match c {
            'I' => 1,
            'V' => 5,
            'X' => 10,
            'L' => 50,
            'C' => 100,
            'D' => 500,
            'M' => 1000,
            _ => 0,
        }
    }).sum()
}
```
Runtime:  3ms  
Memory:  2.04MB  