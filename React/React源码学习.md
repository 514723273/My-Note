react-16.8.4

## 1 React.createElement 过程

1. createElement(type, config, children) type 无变化，处理 config ，从中提取 key、ref、self、source，并封装出 prop。最后调用下面函数。
2. ReactElement(type, key, ref, self, source, owner, props)

### JSX 到 JavaScript 转换 万恶开头

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

### 1.1 packages/react/src/React.js 框架总入口

```js
const React = {
  createElement: __DEV__ ? createElementWithValidation : createElement,   // 这里是引用 ./ReactElement 中的 createElement
}

```

### 1.2 pakages/react/src/ReactElement.js createElement(type, config, children)
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

### 1.3 pakages/react/src/ReactElement.js ReactElement(type, key, ref, self, source, owner, props)

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

### 1.4 pakages/react/src/ReactBaseClasses.js Component(props, context, updater)

```js
const emptyObject = {};

function Component(props, context, updater) {
  this.props = props;
  this.context = context;
  // If a component has string refs, we will assign a different object later.
  this.refs = emptyObject;
  // We initialize the default updater but the real one gets injected by the
  // renderer.
  this.updater = updater || ReactNoopUpdateQueue;
}

Component.prototype.isReactComponent = {};

// setState 只是用来调用 react-dom 里面的函数，并无其他作用！
Component.prototype.setState = function(partialState, callback) {
  // 删去 invariant(...); 只是一个提醒
  this.updater.enqueueSetState(this, partialState, callback, 'setState');   // 这并不是 react 中的，而是 react-dom 中的（因为不同平台
};

// 强制更新
Component.prototype.forceUpdate = function(callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};

// 很简短，生命周期也没有。
```

### 1.5 pakages/react/src/ReactBaseClasses.js PureComponent(props, context, updater)

```js
function ComponentDummy() {}
ComponentDummy.prototype = Component.prototype;

// 和 Component 一模一样
function PureComponent(props, context, updater) {
  this.props = props;
  this.context = context;
  // If a component has string refs, we will assign a different object later.
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

// 继承 Component
const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
// 避免对这些方法进行额外的原型跳转。
Object.assign(pureComponentPrototype, Component.prototype);
// 标识，之后在 react-dom 更新的时候会判断是不是一个 purecomponent
pureComponentPrototype.isPureReactComponent = true;


// 整个文件就导出这两个类
export {Component, PureComponent};
```

### 1.6 packages\react\src\ReactCreateRef.js createRef()

非常简单，只是返回一个对象，存有疑问，如何挂载属性和使用 Ref 。

```js
import type {RefObject} from 'shared/ReactTypes';

// an immutable object with a single mutable value
export function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  if (__DEV__) {
    Object.seal(refObject);
  }
  return refObject;
}
``` 

### 1.7 packages\react\src\ReactContext.js createContext(defaultValue, calculateChangedBits)

```js
// 两个参数 defaultValue calculateChangedBits
export function createContext<T>(
  defaultValue: T,
  calculateChangedBits: ?(a: T, b: T) => number,
): ReactContext<T> {
  // 如果 calculateChangedBits 为 undefined ，则置为 null
  if (calculateChangedBits === undefined) {
    calculateChangedBits = null;
  } else {
    // 删去 __DEV__ 代码
    }
  }

  // 定义 context 对象
  const context: ReactContext<T> = {
    $$typeof: REACT_CONTEXT_TYPE,
    _calculateChangedBits: calculateChangedBits,
    _currentValue: defaultValue,
    _currentValue2: defaultValue,
    _threadCount: 0,
    Provider: (null: any),
    Consumer: (null: any),
    // 删去注解
  };

  context.Provider = {
    $$typeof: REACT_PROVIDER_TYPE,
    _context: context,    // 将 context 挂载到 context.Provider.__context
  };

  // 删去 __DEV__ 代码

  if (__DEV__) {
    // 删去 __DEV__ 代码
  } else {
    context.Consumer = context;   // 将 context 直接挂载到 context.Consumer
  }

  // 删去 __DEV__ 代码

  return context;
}
```

## 2 React中的更新

- ReactDOM.render || hydrate
- setState
- forceUpdate

### 2.1 ReactDOM.render

1. 创建 ReactRoot
2. 创建 FiberRoot 和 RootFiber
3. 创建更新

#### 2.1.1 packages\react-dom\src\client\ReactDOM.js

## 3 Fiber Scheduler

## 4 各类组件的 Update

## 5 完成节点任务

## 6 commitRoots

## 7 功能详解：基础

## 8 suspense and priority

## 9 功能详解：Hooks