## 第10题 正则匹配  

### Rust版本
```rust
pub fn is_match(s: String, p: String) -> bool {
    let (ls, lp) = (s.len(), p.len());
    let mut dp = vec![vec![false; lp+1]; ls+1];
    dp[0][0] = true;
    for j in 0..lp {
        if p.bytes().nth(j).unwrap() == b'*' && dp[0][j-1] {
            dp[0][j+1] = true;
        }
    }
    for i in 0..ls {
        for j in 0..lp {
            match p.bytes().nth(j).unwrap() {
                b'.' => {
                    dp[i+1][j+1] = dp[i][j];
                }
                b'*' => {
                    if p.bytes().nth(j-1).unwrap() != s.bytes().nth(i).unwrap() 
                        && p.bytes().nth(j-1).unwrap() != b'.' {
                            dp[i+1][j+1] = dp[i+1][j-1];
                    } else {
                        dp[i+1][j+1] = dp[i+1][j] || dp[i][j+1] || dp[i+1][j-1];
                    }
                }
                _ => {
                    if s.bytes().nth(i).unwrap() == p.bytes().nth(j).unwrap() {
                        dp[i+1][j+1] = dp[i][j];
                    }
                }
            }
        }
    }
    dp[ls][lp]
}
```
Runtime:  1ms  
Memory:  2.07MB  