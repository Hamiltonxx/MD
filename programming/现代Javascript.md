# module
一个模块就是一个文件，或者一个文件就是一个模块。  
使用export 和 import 来联系彼此。  
```javascript
// sayHi.js
export function sayHi(user) {
  alert(`Hello, ${user}`)
}
```
```javascript
// main.js
import {sayHi} from '.sayHi.js'

alert(sayHi)  // function...
sayHi('John')  // Hello, John 
```
由于modules支持特殊的关键字和特性，我们必须告诉浏览器这个脚本是module脚本。
```html
<!DOCTYPE html>
<script type="module">
  import {sayHi} from './say.js'
  document.body.innerHTML = sayHi('John')
</script>
```
## Module特点
1. Module只在HTTP(s)上work, file://不行  
2. 总是 "user strict"  
3. 有自己的top-level scope.即变量在其它module不可见  
4. module代码只在首次import时执行  
5. module里没有'this'
6. module脚本总是deferred  
