## 第1题
给出整数数组nums和目标整整target, 找出nums中和为target的两个整数, 返回两数下标。  
每组输入只对应一个答案。  
两数顺序任意。
```rust
pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {

}
```
这里要求掌握HashMap的用法
```rust
use std::collections::HashMap;

// pub fn two_sum(nums: &Vec<i32>, target: i32) -> Vec<i32> {
pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut map = HashMap::new();
    for (i,x) in nums.iter().enumerate() {
        let y = target - x;
        if let Some(&j) = map.get(&y) {
            return vec![i as i32, j as i32];
        }
        map.insert(x,i);
    }
    vec![0,0]    
}

fn main() {
    let nums: Vec<i32> = vec![2,7,11,15];
    let target: i32 = 9;
    println!("{:?}", two_sum(nums, target));
    // println!("{:?}", two_sum(&nums, target));
}
```
### accepted
Runtime: 1ms, Beats 87.31%  
Memeory: 2.57MB, Beats 7.31%

## HashMap回顾
### import
```rust
use std::collections::HashMap;
```
### new
```rust
let mut map: HashMap<KeyType, ValueType> = HashMap::new();
```
### insert
```rust
map.insert(key, value);
```
### access
```rust
if let Some(value) = map.get(&key) {
    println!("The value is: {}", value);
}
```
get method returns an Option that contains a reference to the value.
### remove
```rust
map.remove(&key)
```
### iterate
```rust
for (key,value) in &map {
    println!("Key: {}, Value: {}", key, value);
}
```
### others
contains_key, len, clear

## 第2题
给你两个由非负整数构成的非空链表, 数字逆序存储, 每个节点包含一位数字。 把两数相加, 返回链表形式的和。  
2 -> 4 -> 3  
5 -> 6 -> 4
---
7 -> 0 -> 8  

```rust
#[derive(ParitalEq, Eq, Clone, Debug)]
pub struct ListNode {
    pub val: i32,
    pub next: Option<Box<ListNode>>
}
impl ListNode {
    #[inline]
    fn new(val: i32) -> Self {
        ListNode {
            next: None,
            val
        }
    }
}
pub fn add_two_numbers(l1: Option<Box<ListNode>>, l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {

}
```
这题要求掌握智能指针Box, match{Some,None}, Option/Some, as_mut(), unwrap()等要点
```rust
pub fn add_two_numbers(mut l1: Option<Box<ListNode>>, mut l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut carry = 0;
    let mut head = Box::new(ListNode::new(0));
    let mut tail = &mut head;
    while l1!=None || l2!=None || carry!=0 {
        let val = match l1 {
            Some(n) => {l1=n.next;n.val},
            None => 0
        } + match l2 {
            Some(n) => {l2=n.next;n.val},
            None => 0
        } + carry;
        carry = val / 10;
        tail.next = Some(Box::new(ListNode::new(val%10)));
        tail = tail.next.as_mut().unwrap();
    }
    head.next
}
```
尤其注意
```rust
tail.next = Some(Box::new(ListNode::new(val%10)));
tail = tail.next.as_mut().unwrap();
```
这两句的写法  
### Accepted
Runtime: 0ms, Beats 100%  
Memory: 2.26MB, Beats 14.38% 