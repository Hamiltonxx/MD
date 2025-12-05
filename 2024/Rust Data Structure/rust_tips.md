# 交换两个值
```rust
std::mem::swap(&mut num1, &mut num2);
arr.swap(1,3);
vec.swap(1,3);
```

# 最大值最小值
```rust
arr.iter().max().unwrap()
v.iter().min().unwrap()
```
注意返回的是引用

# 数组求和
```rust
arr.iter().sum()
```

# 数组排序
```rust
// 1. sort 简单升序排序
// 2. sort_by 自定义规则排序
// 3. 原地排序
numbers.sort(); // 升序
nums.sort_by(|a,b| b.cmp(a)); // 降序
people.sort_by(|a,b| a.age.cmp(&b.age));
```

# 进制转换
```rust
// 十进制转其他进制
format!("{:b}", 42); // 转二进制
format!("{:o}", 42); // 转八进制
format!("{:x}", 42); // 转十六进制
// 其他进制转十进制
i32::from_str_radix("101010", 2).unwrap();
i32::from_str_radix("52", 8).unwrap();
i32::from_str_radix("2a", 16).unwrap();
```

# sleep
```rust
std::thread::sleep(std::time::Duration::from_secs(10));
```