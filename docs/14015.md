# 重组，但用反应钩！

> 原文：<https://dev.to/cvr/recompose-but-with-react-hooks-4l8p>

[![Hooks](img/d2a3f9bf959c579bcbce629e8663a22a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--imznp9AH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1civ4uqhdt583nfk6l6r.jpg)

当钩子被宣布的时候，我立刻查看了库重组，看看它是否会被一些钩子更新。

不幸的是，该图书馆将被停止..

我决定采用重组的模式，并用钩子来翻译它们。

### 结果:重新增强

```
npm install re-enhance 
```

Enter fullscreen mode Exit fullscreen mode

```
yarn add re-enhance 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

```
import { pipe, usePropMapper, useStateEnhancer /* ... */ } from 're-enhance' 
```

Enter fullscreen mode Exit fullscreen mode

#### 重新构图

```
const BaseComponent = props => {...}

const enhance = compose(
  withState(/*...args*/),
  mapProps(/*...args*/),
  pure
)
const EnhancedComponent = enhance(BaseComponent) 
```

Enter fullscreen mode Exit fullscreen mode

#### 重新增强

```
const useEnhancer = pipe(
    useStateEnhancer(/*...args*/),
    usePropMapper(/*...args*/),
    /*pure cannot be hooked 😔*/
)

// But you can use memo!
const BaseComponent = React.memo(props => {
    const enhancedProps = useEnhancer(props)
    // ...
}) 
```

Enter fullscreen mode Exit fullscreen mode

#### 举例

```
import React from 'react';
import { pipe, useHandlers, useStateEnhancer } from 're-enhance';

const useEnhancer = pipe(
    useStateEnhancer('counter', 'setCounter', 0),
    useHandlers({
        inc: ({ setCounter }) => () => setCounter(counter => counter + 1),
        dec: ({ setCounter }) => () => setCounter(counter => counter - 1),
    }),
);

function Component(props) {
    const { counter, inc, dec } = useEnhancer(props);
    return (
        <div>
            <button onClick={inc}>Inc</button>
            {counter}
            <button onClick={dec}>Dec</button>
        </div>
    );
}

export default Component; 
```

Enter fullscreen mode Exit fullscreen mode

## 需要反馈

由于钩子的限制，使用 React 钩子不能完全移植`recompose`。然而，为了方便起见，我可能会在这个项目中添加一些特设的！

如果你认为可以添加更多的挂钩，请随意投稿！🎉🎉

### 看看吧！

#### [重新增强](https://github.com/cevr/re-enhance)