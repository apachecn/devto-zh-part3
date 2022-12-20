# 使用事件反应天文全局状态

> 原文：<https://dev.to/leonvdw/react-astro---global-state-in-react-using-events-je>

博客 noob 在这里。💩

一天早上，我感到无聊，决定在 react 中开始一个新项目。建立 redux 后，我想尝试一些新的东西。

react 博客最近有一个趋势，解释 react 的新 Hooks API 和实现全局状态的不同方法。有一段时间，我想实现自己定制的全局状态解决方案，并认为这是一个好机会。

这部分是因为我想看看是否有可能使一些东西易于使用和简单设置。

不要误解我，redux 很棒，我不建议在任何大规模项目中使用这个博客中的方法...它还没有经过彻底的测试，纯粹是实验性的，现阶段只是为了好玩！

我在这个定制实现上的主要目标是:

*   真理的单一来源
*   将更新任何使用它的组件的状态
*   无组件钻取(当父组件将数据传递给嵌套很深的子组件时发生，导致 dom 树上类似多米诺骨牌的更新)

我考虑了一会儿，决定了一个可能的解决方案。我认为将“events”中的 EventEmitter 类与一个高阶组件结合使用会提供一个理想的解决方案。

我首先创建了基本结构，以及一个从外部启动 globalState 对象的函数:

```
import React from 'react';
import { EventEmitter } from 'events';

const stateEmitter = new EventEmitter();
stateEmitter.setMaxListeners(0);

export let globalState = {};

export function addGlobalState(stateObj) {
  globalState = { ...globalState, ...stateObj };
} 
```

下一步是创建核心集函数:

```
export const setGlobalState = param => {
  const newState = typeof param === 'function' ? param(globalState) : param;

  try {
    for (var key in newState) {
      globalState[key] = newState[key];
      stateEmitter.emit(`astroUpdate:${key}`);
    }
    stateEmitter.emit('astroUpdateState');
  } catch (error) {
    console.log('setGlobalStateError: ', error);
  }
}; 
```

`setGlobalState`将接受一个普通对象，类似于 react 的`setState`方法(也使用 previousState 实现)。这将使用新的状态对象更新全局状态对象。更新状态后，将发出一个事件，通知订阅的侦听器状态已经更改。

下一步是创建有能力的特设功能:

```
export default function AstroStateProvider(WrappedComponent, keys = []) {
  return class extends React.Component {
    componentDidMount() {
      if (keys.length > 0) {
        for (const key of keys) {
          stateEmitter.on(`astroUpdate:${key}`, () => {
            this.forceUpdate();
          });
        }
      } else {
        stateEmitter.on('astroUpdateState', () => {
          this.forceUpdate();
        });
      }
    }

    render() {
      let stateToReturn = {};
      if (keys.length > 0) {
        for (const key in globalState) {
          if (keys.includes(key)) {
            stateToReturn[key] = globalState[key];
          }
        }
      } else {
        stateToReturn = globalState;
      }

      return <WrappedComponent {...this.props} astro={stateToReturn} />;
    }
  };
} 
```

正如你在上面看到的，特设只是监听任何在`setGlobalState`函数中设置的变化，然后通过`forceUpdate`运行重新渲染。
此外，如`AstroStateProvider`的第二个参数`keys`所定义的，存在仅监听订阅密钥的选项。

然后，hoc 将包装的组件和 globalState 对象的内容返回到一个名为`astro`的 prop 中。

这也是开源贡献的一种尝试，所以我基于这个逻辑创建了一个 npm 包，叫做 [React Astro](https://www.npmjs.com/package/react-astro)

安装、使用和其他小细节可以在那里找到。

也欢迎任何人参与 Github 回购。

干杯🙂