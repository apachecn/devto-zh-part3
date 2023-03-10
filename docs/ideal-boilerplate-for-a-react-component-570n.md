# 可重用 React 组件的理想样板

> 原文：<https://dev.to/itaditya/ideal-boilerplate-for-a-react-component-570n>

从一个功能性的 React 组件开始很容易，但是当我们构建可重用的组件时，我们应该记住一些事情。

1.  我们可以在组件内部呈现自定义标记，使其更加灵活。
2.  我们可以向组件添加类来修改组件的样式。
3.  我们可以设置容器包装的元素的属性。(例如 aria 属性、数据属性)；

为了确保我们的 React 组件能够支持所有这些组件，我们应该使用下面的代码作为样板。

```
import React from 'react';

function ReusableComp({ className, children, ...props }) {
  const classList = ['componentClass1', 'componentClass2', className];

  const classNames = classList.join('  ');
  return <div className={classNames} {...props}>{children}</div>
} 
```

首先我们析构我们组件中需要的所有道具，然后在`props`变量中收集所有剩余的道具。

然后，我们将类名属性与我们自己的组件的类合并。

最后，我们分配类名，并在 div 中展开其他道具和渲染子元素。

我们可以像这样使用它

```
<ReusableComp className='special-item' title='actions'>
  <div>
    <button>Do Something</button>
    <a href="/go-somewhere">Go</a>
  </div>
</ReusableComp> 
```

**PRO 提示**:在我们的组件中，为了避免将 div 元素硬编码为包装器，我们可以像这样在组件中实现`as` prop。

```
import React from 'react';

function ReusableComp({ className, children, as: Element, ...props }) {
  const classList = ['componentClass1', 'componentClass2', className];

  const classNames = classList.join('  ');
  return <Element className={classNames} {...props}>{children}</Element>
} 
```

然后会这样用

```
<ReusableComp as='section' className='special-item' title='actions'>
  <div>
    <button>Do Something</button>
    <a href="/go-somewhere">Go</a>
  </div>
</ReusableComp> 
```