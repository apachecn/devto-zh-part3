# 关于 React 渲染道具 vs HOC

> 原文：<https://dev.to/krzysztofzuraw/on-react-render-props-vs-hoc-1337>

如果你想比 dev.to 上的帖子早一周得到这些帖子，你可以在 https://krzysztofzuraw.com/newsletter 的上加入我的时事通讯。

最近，我的一个同事在使用一种叫做 [hoc](https://reactjs.org/docs/higher-order-components.html) (高阶组件)的流行反应模式时，偶然发现了奇怪的情况。事实证明，有时您的 hoc 可以覆盖您通过的`props`。让我再解释一下。

假设您有一个`Button`组件:

```
import * as React from 'react';

interface OwnProps {
  onClick: () => void;
  amount: number;
  name: string;
}

const Button: React.FunctionComponent<OwnProps> = props => (
  <button onClick={props.onClick}>
    {props.name} {props.amount}
  </button>
); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个需要 3 个道具的小组件。一个`onClick`用于处理按钮点击，其余两个仅用于在按钮上显示数据。

你怎么能把道具传给这个按钮呢？让我们同意你需要一个更高层次的抽象😂你通过`HOC` :
传递它们

```
import * as React from 'react';

export interface HOCProps {
  onClick: () => void;
}

export const withHOC = <WrappedComponentProps extends object>(
  WrappedComponent: React.ComponentType<WrappedComponentProps>
) => {
  // tslint:disable-next-line:no-console
  const onClick = () => console.log('Clicked! from HOC');

  const HOC: React.FunctionComponent<WrappedComponentProps & HOCProps> = props => {
    return <WrappedComponent {...props} onClick={onClick} />;
  };
  HOC.displayName = `withHOC(${WrappedComponent})`;
  return HOC;
}; 
```

Enter fullscreen mode Exit fullscreen mode

所以这个可以这样用:

```
import * as React from 'react';

import { HOCProps, withHOC } from '../hoc';
import { Button } from './button';

interface OwnProps {
  onClick: () => void;
  amount: number;
  name: string;
}

const ButtonHOC = withHOC(Button);

// usage
<ButtonHOC onClick={() => setAmount(amount + 1)} amount={amount} name="HOC button" />; 
```

Enter fullscreen mode Exit fullscreen mode

你为自己如此出色的工作而感到自豪-👏🏻。你抽象开`onClick`。

代码看起来很好，但是点击按钮会导致`console.log`！但是你想让它增加 1。发生了什么事？

你的 HOC 正在覆盖你的组件道具。为了避免这种情况，你需要改变道具名称——这样冲突就不会再发生了。

让我们寻找另一种在 react world 中常见的模式——渲染道具:

```
import * as React from 'react';

export interface OwnProps {
  render: ({ onClick }: { onClick: () => void }) => JSX.Element;
}

export const RenderPropsButton: React.FunctionComponent<OwnProps> = props => {
  // tslint:disable-next-line:no-console
  const onClick = () => console.log('Clicked from renderProps');
  return props.render({ onClick });
}; 
```

Enter fullscreen mode Exit fullscreen mode

可以这样用:

```
<RenderPropsButton
  render={renderProps => (
    <Button onClick={renderProps.onClick} amount={amount} name="RenderProps button" />
  )}
/> 
```

Enter fullscreen mode Exit fullscreen mode

有帮助吗？是的——因为我们是通过`renderProp`
传递`onClick`，而不是直接注入到组件中。这允许开发者看到这个道具是从哪里来的，并修复它。

## 总结

当渲染道具第一次出现在 react world 时，我并没有看到它比 hoc 有明显的优势。在看到类似上面的例子后，我发现它们在可读性方面比 hoc 更好。

PS。我知道这些例子过于复杂。这里有证明这一点。

### TLDR

使用渲染道具，您可以避免意外的道具覆盖。

如果你想比 dev.to 上的帖子早一周得到这些帖子，你可以在 https://krzysztofzuraw.com/newsletter 的上加入我的时事通讯。