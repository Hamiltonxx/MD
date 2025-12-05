## 第10题 正则匹配
给定string s 和 pattern p, 输出是否匹配.  p支持  
1. '.' 匹配任何单一字符  
2. '*' 匹配0个或多个前置元素

"aa"  "a"  ->  false  
"aa"  "a*"  ->  true  
"ab"  ".*"  ->  true  
"aab"  "c*a*b"  -> true  
"mississippi"  "mis*is*p*."  ->  false  

### 打表
|   |   |   | c | * | a | * | b |
|---|---|---|---|---|---|---|---|
|   |   | 0 | 1 | 2 | 3 | 4 | 5 |
|   | 0 | T | F | T | F | T | F |
| a | 1 | F | F | F | T | T | F |
| a | 2 | F | F | F | F | T | F |
| b | 3 | F | F | F | F | F | T |

### Python版本
```python
def isMatch(s:str, p:str) -> bool:
	m, n = len(s), len(p)
	DP = [[False for j in range(n+1)] for i in range(m+1)]
	DP[0][0] = True 
	for i in range(m+1):
		for j in range(1, n+1):
			if p[j-1]=="*":
				DP[i][j] = DP[i][j-2] or i>0 and DP[i-1][j] and (s[i-1]==p[j-2] or p[j-2]==".")
			else:
				DP[i][j] = i>0 and DP[i-1][j-1] and (s[i-1]==p[j-1] or p[j-1]==".")
	return DP[-1][-1]
```
Runtime:  47ms  
Memory:  16.54MB  
