# 前言

## 作者

Robin Wieruch, 德国全栈开发。

## 简介

"The Road to React"自 2016 年发布，几乎每年都重写一版。本书只讲基础。

## 代码

不要满足于跑书里代码，自己实践修改代码，并验证结果的变化。

# Hello React

## 作业

https://www.robinwieruch.de/web-applications/

## first run

```
cd hacker-stories
npm create vite@latest .
```

## 工程目录

src/App.jsx 实现 React component. 稍后分解 React component 成更多文件/component.  
src/main.jsx React 世界的入口文件。  
src/index.css 和 src/App.css project 和 component 的 css 文件。

# 查看 React Component

```javascript
import { useState } from 'react';
import reactLogo './assets/react.svg';
import viteLogo from '/vite.svg';
import './App.css';

function App() {
    const [count, setCount] = useState(0);

    return (
        <>
            <div>
                <a href="https://vite.dev" target="_blank">
                    <img src={viteLog} className="logo" alt="Vite logo" />
                </a>
                <a href="https://react.dev" target="_blank">
                    <img src={reactLogo} className="logo react" alt="React logo" />
                </a>
            </div>
            <h1>Vite + React</h1>
            <div className="card">
                <button onClick={() => setCount((count) => count + 1)}>count is {count}</button>
                <p>Edit <code>src/App.jsx</code> and save to test HMR</p>
            </div>
            <p className='read-the-docs'>Click on the Vite and React logos to learn more</p>
        <>

    );
}

export default App;
```

注意:

1. setCount((count)=>count+1), 根据之前 state 计算新的 state,请用函数式更新
2. named export vs default export. 如果 React 内部库是

```javascript
export function useState() {...}
export function useEffect() {...}
```

它不是默认导出，所以导入时必须用大括号  
如果库是默认导出

```javascript
export default reactLogo;
```

那么可以不用大括号，甚至可以随便命名。

3. App component 也只是一个 javascript function, 但必须首字母大写。

# JSX

## 面试题

jsx 可以直接被浏览器渲染吗？  
不能！ 需要先转译成 javascript.

# React list

```javascript
const list = [
  {
    title: 'React',
    url: 'https://react.dev/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  },
  {
    title: 'Redux',
    url: 'https://redux.js.org/',
    author: 'Dan Abramov, Andrew Clark',
    num_comments: 2,
    points: 5,
    objectID: 1,
  },
];

return (
    <ul>
        {list.map(function(item) {
            return (
                <li key={item.objectID}>
                    <span><a href={item.url}>{item.title}</a>
                    <span>{item.author}</span>
                    <span>{item.num_comments}</span>
                    <span>{item.points}</span>
                </li>
            );
        })}
    </ul>
)
```

# 另一个 component

```javascript
function App() {
  return (
    <>
      <h1>My Hacker Stories</h1>
      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

      <hr />

      <List />
    </>
  );
}

function List() {
  return (
    <ul>
      {list.map(function (item) {
        return (
          <li key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
          </li>
        );
      })}
    </ul>
  );
}
```

# React DOM

App component 的声明和实例化, 看 src/main.jsx

```javascript
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import 'index.css';
import App from './App.jsx';

createRoot(document.getElementById('root)).render(
  <StrictMode>
    <App />
  </StrictMode>
)
```

查看 index.html

```html
<body>
  <div id="root"></div>
  <script type="module" src="/src/main.jsx"></script>
</body>
```

# React component 声明和实例化

如果箭头函数只返回一个值而没有别的逻辑，那么可以去掉 {} 和 return，使用简洁写法。

```jsx
const App = () => (
  <>
    <Search />
    <hr />
    <List />
  </>
);
const Search = () => <></>;
const List = () => (
  <ul>
    {list.map((item) => (
      <li key={item.objectID}>...</li>
    ))}
  </ul>
);
```

# Handler Function

## Synthetic Event

SyntheticEvent = React 自己封装的事件对象，用来实现跨浏览器统一 & 性能优化(事件委托)。  
你写的 onClick/onChange 收到的不是原生事件，而是 React 的合成事件。

```jsx
const Search = () => {
  const handleChange = (event) => {
    console.log(event);
    console.log(event.target.value);
  }
  return (
    <label htmlFor="search">Search: </label>
    <input id="search" type="text" onChange={handleChange} />
  );
}
```

# React Props
