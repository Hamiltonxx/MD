# 第1题
给出整数数组nums和目标整整target, 找出nums中和为target的两个整数, 返回两数下标。  
每组输入只对应一个答案。  
两数顺序任意。

```rust
fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut map:HashMap<i32,usize> = HashMap::new();
    for (i,&x) in nums.iter().enumerate() {
        let y = target - x;
        if let Some(&j) = map.get(&y){
            return vec![i as i32, j as i32];
        }
        map.insert(x, i);
    }
    vec![0,0]
}
```

# accepted
Runtime: 0ms, Beats 100.00%  
Memeory: 2.63MB, Beats 10.33%

# 附HashMap用法
```rust
use std::collections::HashMap;

fn main() {
    let mut map:HashMap<&str,i32> = HashMap::new();

    map.insert("apple", 3);
    map.insert("banana", 5);

    match map.get("apple") {
        Some(&count) => println!("苹果的数量: {count}"),
        None => println!("没有找到苹果"),
    }

    for (key, value) in &map {
        println!("{}: {}", key, value);
    }

    map.remove("banana");
    println!("{:?}", map);

    if let Some(&count) = map.get("apple") { // &str
        println!("apple count: {count}");
    }
}
```