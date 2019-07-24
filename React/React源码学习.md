## 1 JSX 到 JavaScript 转换

```js
function Comp() {
  return <a>com</a>
}

<Com id="div" key="key">
  <span>1</span>
  <span>2</span>
</Com>
```

将上述 JSX 语法转化为 JS 语法。

```js
"use strict";

function Comp() {
  return React.createElement("a", null, "com");
}

React.createElement(Com, {
  id: "div",
  key: "key"
}, React.createElement("span", null, "1"), React.createElement("span", null, "2"));
```

## 2 