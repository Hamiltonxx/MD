## 第4题 两个排好序的数组找中位数
```rust
pub fn find_median_sorted_arrays(nums1: Vec<i32>, nums2: Vec<i32>) -> f64 {

}
```
要求最大时间复杂度 O(log(m+n))
```rust
pub fn find_median_sorted_arrays(nums1: Vec<i32>, nums2: Vec<i32>) -> f64 {
    let (mut nums1, mut nums2) = if nums1.len() <= nums2.len() {
        (nums1, nums2)
    } else {
        (nums2, nums1)
    };
    let m = nums1.len();
    let n = nums2.len();
    let total = m + n;
    let half = total / 2;

    let mut left = 0;
    let mut right = m;
    while left <= right {
        let i = (left + right) / 2;
        let j = half - i;
        let nums1_left_max = if i==0 { i32::MIN } else { nums1[i-1] };
        let nums1_right_min = if i==m { i32::MAX } else { nums1[i] };
        let nums2_left_max = if j==0 { i32::MIN } else { nums2[j-1] };
        let nums2_right_min = if j==n { i32::MAX } else { nums2[j] };

        if nums1_left_max > nums2_right_min {
            right = i - 1;
        } else if nums2_left_max > nums1_right_min {
            left = i + 1;
        } else {
            let max_of_left = nums1_left_max.max(nums2_left_max);
            let min_of_right = nums1_right_min.min(nums2_right_min);
            if total % 2 == 0 {
                return (max_of_left + min_of_right) as f64 / 2.0;
            } else {
                return min_of_right as f64;
            }
        }
    }
    0.0
}
```

Runtime: 0ms  
Memory: 2.12MB  

## 第5题 最长回文子串
babad -> bab or aba  
cbbd -> bb

### 暴力解法
python版本  
```python
def longestPalindrome(s:str) -> str:
    cand = {}
    for i in range(len(s)):
        for j in range(i+1, len(s)+1):
            if s[i:j][::-1] == s[i:j]:
                cand[s[i:j]] = len(s[i:j])
    return max(cand, key=cand.get)
```
```python
def longestPalindrome(s:str) -> str:
    mxl, mxs = 1, s[0]
    for i in range(len(s)):
        for j in range(i+1, len(s)+1):
            if s[i:j][::-1] == s[i:j] and j-i+1 > mxl:
                mxl, mxs = j-i+1, s[i:j]
    return mxs
```
Runtime: 8846ms  
Memory: 16.70MB

对应的rust版本  
```rust
pub fn longest_palindrome(s: String) -> String {
    let mut l = 1;
    let (mut start, mut end) = (0, 1);
    for i in 0..s.len() {
        for j in i+1..=s.len() {
            let substr = &s[i..j];
            let reversed = substr.chars().rev().collect::<String>();
            if reversed == substr && j-i+1>l {
                l = j-i+1;
                (start,end) = (i,j);
            }
        }
    }
    s[start..end].to_string()
}
```
Time Limit Exceeded  


### Expand Around Center
```python
def longestPalindrome(s:str) -> str:
    n = len(s)
    def expand(l,r):
        while l>=0 and r<n and s[l]==s[r]:
            l -= 1
            r += 1
        return s[l+1:r]
    mx = ''
    for i in range(n):
        ans1 = expand(i, i) # odd length
        ans2 = expand(i, i+1) # even length
        mx = max([mx, ans1, ans2], key=len)
    return mx
```
Runtime: 235ms  
Memory: 16.56MB  

```rust
fn expand_around_center(t: &str, mut left: usize, mut right: usize) -> (usize, usize) {
    let chars: Vec<char> = t.chars().collect();
    while left > 0 && right < chars.len() && chars[left - 1] == chars[right] {
        left -= 1;
        right += 1;
    }
    (left, right)
}

pub fn longest_palindrome(s: String) -> String {
    let n = s.len();
    let (mut start, mut end) = (0, 0);
    for i in 0..n {
        let (l1, r1) = expand_around_center(&s, i, i);
        let (l2, r2) = expand_around_center(&s, i, i + 1);
        if r1 - l1 > end - start {
            start = l1;
            end = r1;
        }
        if r2 - l2 > end - start {
            start = l2;
            end = r2;
        }
    }
    s[start..end].to_string()
}
```
Runtime: 84ms  
Memory: 2.16MB  

Expand Around Center的时间复杂度是 O(N^2), 后面要再看下 O(N)的 Manacher算法。