# 反应模式问题

> 原文：<https://dev.to/joshlove/react-pattern-question-2coc>

你好，

我一直在尝试新的 React 挂钩，最终我发现一个组件有自己的存储和动作，父组件可以根据需要访问它们。

用例:需要显示来自我的组件的数据，或者能够触发来自父组件的功能(无需通过像 redux 这样的库)。

我的问题是:对于 React 中的一个组件来说，这是一个好的设置吗？还是一个反模式？

代码([工作密码](https://jsfiddle.net/k5yhw3at/1/) ):

```
import React, { useState } from 'react';

const ChildComponent = (prop1) => {
  const [myCustomValue, updateMyCustomValue] = useState(0);

  const myCustomAction = () => {
    updateMyCustomValue(myCustomValue + 1);
  }

  const view = () => (
    <input type="button" onClick={myCustomAction} value={`${myCustomValue} - ${prop1}`} />
  );

  return {
    store: myCustomValue,
    actions: myCustomAction,
    view,
  };
};

const ParentComponent = () => {
  const prop1 = 'test';

  const Test1 = ChildComponent(prop1);

  return (
    <div>
      <div>
        <h2>Component Default View</h2>
        <Test1.view />
        <h2>Parent can also trigger updates and show data from component</h2>
        <input type="button" onClick={Test1.actions} value={Test1.store} />
      </div>
    </div>
  );
} 
```