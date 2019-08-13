> 摘录自 [creeperyang/blog/比较与理解React的Components，Elements和Instances](https://github.com/creeperyang/blog/issues/30)

# 比较与理解React的Components，Elements和Instances

## 1 从JSX出发

那么，`React.createElement`是在做什么？看下相关部分代码：
```js
var ReactElement = function(type, key, ref, self, source, owner, props) {
  var element = {
    // This tag allow us to uniquely identify this as a React Element
    $$typeof: REACT_ELEMENT_TYPE,

    // Built-in properties that belong on the element
    type: type,     // ** 这个对象大概是这样的
    key: key,       // ** 这个对象大概是这样的
    ref: ref,
    props: props,   // ** 这个对象大概是这样的  // 里面可以有 children

    // Record the component responsible for creating this element.
    _owner: owner,
  };
  // ...
  return element;
};

ReactElement.createElement = function(type, config, children) {
  // ...
  return ReactElement(
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current,
    props
  );
};
```
看起来，`ReactElement.createElement`最终返回了一个对象，这个对象大概是这样的形状：

```js
// 非常明显，这就是一个Elements Tree！
{
  type,
  key,
  props: {
    children
  }
}
```

## 2 React 用 Elements Tree 描述 UI

一个元素（element）就是一个**纯对象**，描述了一个组件实例或DOM node，以及它需要的属性。它仅仅包含这些信息：组件类型，属性（properties），及子元素。

**元素不是实例**，实际上，它更像是告诉React你需要在屏幕上显示什么的一种方式。它就是一个有2个数据域（field）的不可变描述对象（immutable description object）：
```js
// 少个 key
{
  type: (string | ReactClass),
  props: Object
}
```

## 3 DOM Elements

当元素的type是string时，那么这个元素就表示一个DOM node（type的值就是tagName，props就是attributes）。 这node就是React将渲染的。比如：

```js
{
  type: 'button',
  props: {
    className: 'button button-blue',
    children: {     // 注意下元素是怎么嵌套的。当我们想创建元素树时，我们设置children属性。
      type: 'b',
      props: {
        children: 'OK!'
      }
    }
  }
}
```
将被渲染成：
```html
<button class='button button-blue'>
  <b>
    OK!
  </b>
</button>
```

**注意：子元素和父元素都只是描述，并不是实际的实例。当你创建它们的时候，它们并不指向屏幕上的任何东西。 显然，它们比DOM轻量多了，它们只是对象。**

## 4 Component Elements

此外，元素的type也可以是function或者class（即对应的React Component）：
```js
{
  type: Button,
  props: {
    color: 'blue',
    children: 'OK!'
  }
}
```

你可以混合搭配 DOM 和 Component Elements：
```js
const DeleteAccount = () => ({
  type: 'div',          // 最外层 DOM Element
  props: {
    children: [{
      type: 'p',        // 第一个子节点 DOM Element
      props: {
        children: 'Are you sure?'
      }
    }, {
      type: DangerButton,       // 第二个子节点 Component Element
      props: {
        children: 'Yep'
      }
    }, {
      type: Button,             // // 第三个子节点 Component Element
      props: {
        color: 'blue',
        children: 'Cancel'
      }
   }]
 }
});
```

或者，如果你更喜欢JSX：
```js
const DeleteAccount = () => (
  <div>
    <p>Are you sure?</p>
    <DangerButton>Yep</DangerButton>
    <Button color='blue'>Cancel</Button>
  </div>
);
```

## 5 Components Encapsulate Element Trees

当React碰到type是function|class时，它就知道这是个组件了，它会问这个组件："给你适当的props，你返回什么元素（树）？"。

比如当它看到：
```js
{
  type: Button,
  props: {
    color: 'blue',
    children: 'OK!'
  }
}
```

React会问 Button 要渲染什么，Button返回（整个替换）：
```js
{
  type: 'button',
  props: {
    className: 'button button-blue',
    children: {
      type: 'b',
      props: {
        children: 'OK!'
      }
    }
  }
}
```
React会重复这种过程，直到它知道页面上所有的组件想渲染出什么DOM nodes。

对React组件来说，props是输入，元素树（Elements tree）是输出。

我们选择让React来 创建，更新，销毁 **实例**，我们用**元素**来描述它们，而React负责管理这些实例。

## 6 总结

react组件就两个结构：类型和**属性**。 事件和 children 都是**属性**。

仅仅*以类声明的组件才有实例*，并且你从来不会直接创建它——**React为你创建它**。（实例没用，可能只是被 React 实例化一次后，只是拿来调方法。（毕竟不能拿着类调方法吧））

React组件和React元素关系密切，**React组件**最核心的作用是**返回React元素**。

React 组件 render 函数中 return 一个 React.createElement(type, config, children) --- React 元素。

React组件负责调用React.createElement()，返回React元素，供 React 内部将其渲染成最终的页面DOM 提供结构参考。React 组件只是调用 `React.createElement()` 的“责任人”而已。（整个返回的 js 对象（React 元素），就是 虚拟 DOM）