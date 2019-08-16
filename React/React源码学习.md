react-16.8.4

# React.createElement 过程

1. createElement(type, config, children) type 无变化，处理 config ，从中提取 key、ref、self、source，并封装出 prop。最后调用下面函数。
2. ReactElement(type, key, ref, self, source, owner, props)

## JSX 到 JavaScript 转换 万恶开头

```js
function Comp() {
  return <a>com</a>
}

<Com id="com1" key="key1">
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

// 这边可以引出一个问题，为什么 React 要求自定义组件大写开头？
// 对比上面的"a"，可以发现，babel 是根据单词开头大小写，来区分是原生组件还是自定义组件。（否则运行时报错
React.createElement(
  Comp,                    
  { id: "com1", key: "key1"}, 
  React.createElement("span", null, "1"), 
  React.createElement("span", null, "2")
);
```

## packages/react/src/React.js 框架总入口

```js
const React = {
  createElement: __DEV__ ? createElementWithValidation : createElement,   // 这里是引用 ./ReactElement 中的 createElement
}

```

## pakages/react/src/ReactElement.js createElement(type, config, children)
```js
import ReactCurrentOwner from './ReactCurrentOwner';

const RESERVED_PROPS = {
  key: true,
  ref: true,
  __self: true,
  __source: true,
};

export function createElement(type, config, children) {
  // 遍历用
  let propName;

  // Reserved names are extracted
  const props = {};

  // 这四个值从 config 单独抽离赋值，不放入 props
  let key = null;
  let ref = null;
  let self = null;
  let source = null;

/*
* ↑----------------- 0. 变量定义 -----------------↑
*/

  if (config != null) {
    // 抽离有效的 ref 1
    if (hasValidRef(config)) {
      ref = config.ref;
    }
    // 抽离有效的 key 2
    if (hasValidKey(config)) {
      key = '' + config.key;
    }
    // 抽离 self 3
    self = config.__self === undefined ? null : config.__self;
    // 抽离 source 4
    source = config.__source === undefined ? null : config.__source;

/*
* ↑----------------- 1. 从 config 抽离单独赋值 key ref self source -----------------↑
*/

    // 遍历 config 将所有的属性赋值给开头也是最后的 props
    // for-in 循环时，返回实例属性和原型属性。（能够通过对象访问的、可枚举的属性）
    for (propName in config) {
      // hasOwnProperty 只看实例，不看原型
      // if(config 实例非原型有这个属性 && 这个属性不是 key、ref、__self、__source 四个中的任意一个)
      if (
        hasOwnProperty.call(config, propName) &&
        !RESERVED_PROPS.hasOwnProperty(propName)
      ) {
        props[propName] = config[propName];
      }
    }
  }

/*
* ↑----------------- 2. 将剩余的 config 赋值给 props -----------------↑
*/

  // Children can be more than one argument, and those are transferred onto
  // the newly allocated props object.
  const childrenLength = arguments.length - 2;
  if (childrenLength === 1) {
    props.children = children;
  } else if (childrenLength > 1) {
    const childArray = Array(childrenLength);
    for (let i = 0; i < childrenLength; i++) {
      childArray[i] = arguments[i + 2];
    }
    if (__DEV__) {
      if (Object.freeze) {
        Object.freeze(childArray);
      }
    }
    props.children = childArray;
  }

/*
* ↑----------------- 3. 将所有子组件整合成数组，赋值给 props.children -----------------↑
*/

  // Resolve default props
  // 例如上例 type=Comp，type.defaultProps 就等于 Comp.defaultProps 静态的初始 props

  // 如果是自定义组件
  if (type && type.defaultProps) {
    const defaultProps = type.defaultProps;
    for (propName in defaultProps) {
      // 给没有赋值的属性，赋默认值
      if (props[propName] === undefined) {
        props[propName] = defaultProps[propName];
      }
    }
  }

/*
* ↑----------------- 4. 若是自定义组件，将 props 中未赋值的属性赋默认值 -----------------↑
*/

  // 删去了中间的 __DEV__ 开发代码

  // 上面做了这么多，只是为了构造 ReactElement() 的参数。
  return ReactElement(
    type,   // 取自 函数第一个参数 type
    key,    // 取自 config.key
    ref,    // 取自 config.ref
    self,   // 取自 config.__self
    source, // 取自 config.__source
    ReactCurrentOwner.current,    // 好像和 fiber 相关，才27行代码{???}
    props,  // 上述 2,3,4 步骤构造出的 props
  );
}
```

##

```js
import {REACT_ELEMENT_TYPE} from 'shared/ReactSymbols';   // Symbol

// 整个函数只是对参数进行再细一点点的处理，对应赋值，返回一个普通对象。
// 应该这就是 虚拟 DOM
const ReactElement = function(type, key, ref, self, source, owner, props) {
  const element = {
    // This tag allows us to uniquely identify this as a React Element
    // 标识 element 是什么类型的 // 如果都是通过 React.createElement 创建的，那就永远是 REACT_ELEMENT_TYPE
    // 感觉我们都是用 jsx 也就是 React.createElement ，大部分 $$typeof 都相同为此值（可能有特殊情况，和平台有关
    $$typeof: REACT_ELEMENT_TYPE,

    // Built-in properties that belong on the element
    type: type,
    key: key,
    ref: ref,
    props: props,

    // Record the component responsible for creating this element.
    _owner: owner,
  };

  // 删去了中间的 __DEV__ 开发代码 // 其中用到了 source， self

  return element;
};
```