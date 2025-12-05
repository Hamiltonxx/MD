## 第3题 最长无重复子串
给定字符串s,找出没有重复字符的最长子串的长度。 
abc -> 3  
abcdbe -> 4  
pqabcadxyzaf -> 7  
bbbb -> 1  
"" -> 0  
" " -> 1  

### python版本
滑动窗口和set
```python
def lengthOfLongestSubstring(s: str) -> int:
    l = mx = 0
    st = set()
    for r,x in enumerate(s):
        while x in st:
            st.remove(s[l])
            l += 1
        st.add(x)
        mx = max(mx, r-l+1)
    return mx
```
滑动窗口和hash
```python
def lengthOfLongestSubstring(s: str) -> int:
    l = mx = 0
    lastpos = {}
    for r,x in enumerate(s):
        if x in lastpos and lastpos[x] >= l:
            l = lastpos[x] + 1
        lastpos[x] = r
        mx = max(r-l+1)
    return mx
```
Runtime: 38ms  
Memory: 16.65MB
  

### rust版本

```rust
use std::cmp::max;
pub fn length_of_longest_substring(s: String) -> i32 {
    let mut mx = 0;
    let mut l = 0;
    let mut index = vec![0; 128];

    for (r,x) in s.chars().enumerate() {
        l = max(index[x as usize], l);
        mx = max(mx, r-l+1);
        index[x as usize] = r+1;
    }

    mx as i32
}
```
rust uses utf8 which compatible with ascii  
128 characters can use 128bit int like "hashtable" to store chars  

Runtime: 0ms
Memory: 2.13MB  

