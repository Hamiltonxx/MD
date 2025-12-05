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

### Python版本
```python
def romanToInt(s:str) -> int:
	d = {'I':1,'V':5,'X':10,'L':50,'C':100,'D':500,'M':1000}
	res = 0
	for i in range(len(s)-1):
		if s[i:i+2] in ('IV','IX','XL','XC','CD','CM'):
			res -= d[s[i]]
		else:
			res += d[s[i]]
	res += d[s[-1]]
	return res
```
Runtime:  41ms  
Memory:  16.52MB  
