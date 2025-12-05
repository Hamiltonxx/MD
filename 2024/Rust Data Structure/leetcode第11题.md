## 第11题 面积最大的容器
height = [1,8,6,2,5,4,8,3,7]  ->  49  
height = [1,1]  ->  1  

![](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

### Python版本
```python
def maxArea(height: List[int]) -> int:
	mx = 0 
	l = 0 
	r = len(height) - 1
	while l < r:
		mx = max(mx, min(height[l],height[r])*(r-l))
		if height[l] < height[r]:
			l += 1
		else:
			r -= 1
	return mx
```
Runtime:  546ms  
Memory:  29.54MB  

### Rust版本
```rust
use std:cmp:min;
pub fn max_area(height: Vec<i32>) -> i32 {
    let mut mx = 0;
    let mut l = 0;
    let mut r = height.len() - 1;
    while l < r {
        let mn = min(height[l],height[r]) as usize;
        mx = if mn * (r-l) > mx {
            mn * (r-l)
        } else {
            mx
        };
        if height[l] < height[r] {
            l += 1
        } else {
            r -= 1
        }
    }
    mx as i32
}
```
Runtime:  4ms  
Memory:  2.94MB