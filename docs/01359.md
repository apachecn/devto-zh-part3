# Javascript / Typescript 中的高阶组件

> 原文：<https://dev.to/stephencweiss/higher-order-components-in-javascript-typescript-3f35>

假设您有一个想要一直传递到 DOM 的道具。如果你试图通过一个语义不正确的道具(即没有使用“烤肉串盒”)，React 会对你大喊大叫。

一种处理方法是使用高阶元件(HOC)。hoc 是返回组件的组件。另一种看待它们的方式是“包装”——所以我在这里称我的为“包装”。

让我们来看一个例子，在这个例子中，我们使用一个 HOC，`Wrapper`，来查看被传入的道具，并确定要做什么。

```
import React from 'react';
import PropTypes from 'prop-types';

export type WrapperProps = { special-prop?: string; specialProp?: string };
const Wrapper = <P extends WrapperProps>(WrappedComponent: React.ComponentType<P>): React.FunctionComponent<P> => {
    const Wrapper = (props: P) => {
        return <WrappedComponent data-special-prop={props['special-prop'] || props.specialProp} {...props} />;
    };
};

export default Wrapper; 
```

好的一面是，如果`’special-prop'`和`specialProp`都不存在，`data-special-prop`就不会通过。

有时这与道具无关，而是取决于特定道具(或一组道具)的不同行为。

让我们举一个例子，如果我们有一个`specialProp`我们想让背景变成粉红色。

```
import React from 'react';

export type ConditionalWrapperProps = { specialProp?: string };
const Wrapper = <P extends WrapperProps>(WrappedComponent: React.ComponentType<P>): React.FunctionComponent<P> => {
    const Wrapper = (props: P) => {
        return (
          specialProp ? (
            <div style={{background: 'pink'}}>
              <WrappedComponent {...props.style} {...props} />
            </div> )
          : <WrappedComponent {...props.style} {...props} />
    };
};

export default Wrapper; 
```

# 使用特设

既然我们已经定义了 hoc，我们就必须使用它们。

在我们的例子中，我们将特设保存到同一个目录中的一个文件中，`higher-order-components.tsx`。

我们可以导入该模块，并将其作为另一个组件的包装器，这样无论何时调用`MyComponent`，我们都可以通过`Wrapper`传递它(注意最后一行)。

```
import React from 'react'
import Wrapper from './higher-order-components'

const MyComponent = () => {
  ;<React.Fragment>...</React.Fragment>
}
export default Wrapper(MyComponent) 
```