## 第14题 最长公共前缀
输入字符串数组，输出最长公共前缀。  
"flower","flow","flight"  ->  "fl"  
"dog","racecar","car"  ->  ""

### Python版本
```python
def longestCommonPrefix(strs: List[str]) -> str:
    mn, mx = min(strs), max(strs)
    for i in range(len(mn)):
        if mn[i] != mx[i]:
            return mn[:i]
    return mn
```
Runtime:  25ms  
Memory:  16.56MB  

### Rust版本
```rust
pub fn longest_common_prefix(strs: Vec<String>) -> String {
    let mn = strs.iter().min().unwrap();
    let mx = strs.iter().max().unwrap();
    for i in 0..mn.len() {
        if mn.as_bytes()[i] != mx.as_bytes()[i] {
            return mn[..i].to_string();
        }
    }
    mn.to_string()
}
```
Runtime:  1ms  
Memory:  2.03MB
