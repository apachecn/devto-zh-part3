# 😜我对⚓·胡克斯的看法

> 原文：<https://dev.to/droidmakk/my-looks-on-hooks-36oc>

> 很久以后我最爱的一篇文章。你不需要职业来维持未来的状态

### 功能性有状态组件🎡

那是一个词吗？

所以这里我们有一种传统的方式来创建有状态的组件，比如`Class App extends`等等等等...

但不是这样，这里我们有功能性的有状态组件，如下所示。

```
import React, { useState } from 'react'; //Importing modules

function App() {

  const [date, updateDate] = React.useState(Date());

  return (
    <div className="App">
      <h1>{date}</h1>
      <button onClick={() => {
        updateDate(Date())
      }}> Update </button>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

酷啊...😁

## 使用效果

不是这个...我们的壁橱里还有更酷的东西。既然我们知道了如何在功能组件中维护状态。如果部件中有一些影响，我们该如何反应...🤔真的只是`useEffect`😜。

让我们来处理有效的变化。

```
import React, { useState, useEffect } from 'react'; //Importing modules

function App() {

  let count_p = React.createRef();

  const [date, updateDate] = React.useState(Date());

  React.useEffect(() => {
    count_p.current.textContent = Number(count_p.current.textContent)+1;
  });

  return (
    <div className="App">
      <h1>{date}</h1>
      <button
        onClick={() => {
          updateDate(Date());
        }}
      >
        {"  "}
        Update{"  "}
      </button>
      <p>You've updated dated <span ref={count_p} ></span> time(s)</p>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

所以每次更新 state 时，都会调用方法`useEffect`。我们走吧。
我们在这里使用了 Ref，有更好的方法吗？🙄

## useRef

从上面的例子中我们可以使用另一个钩子。`useRef`。让我们利用这一点。T3】

```
import React, { useState, useEffect, useRef } from 'react'; //Importing modules

function App() {

  let count_p = useRef(null);

  const [date, updateDate] = React.useState(Date());

  useEffect(() => {
    count_p.current.textContent = Number(count_p.current.textContent)+1;
  });

  return (
    <div className="App">
      <h1>{date}</h1>
      <button
        onClick={() => {
          updateDate(Date());
        }}
      >
        Update
      </button>
      <p>You've updated dated <span ref={count_p} ></span> time(s)</p>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用上下文

因此，发布的上下文已成为遗留上下文。现在有了一种新的写法。假设我有一个上下文，叫做定量上下文。在这张单子上，我每年都添加新的定量。一旦我这样做了，我希望它能被列在所有的配给商店里。所以我们用了一个叫做**的东西来描述**。让我们看看它是如何工作的。

```
// RationContext.js
import React,{ Component, createContext } from 'react';

// Create a context which gives Provider and Consumer
const RationContext = React.createContext({
    //Add initial values
    ration: [],
    addRation: () => null
});

//export the consumer
export RationConsumer = RationContext.Consumer;

//export the provider with state and other methods
export class RationProvider extends Component {

    constructor(props){
        super(props);
        this.addRation = this.addRation.bind(this);
        this.state = { ration: [] }
    }

    addRation = (ration) => {
        let { ration } = this.state;
        ration.push(ration);
        this.setState({ ration });
    }

    render(){
        let { ration } = this.state;
        let { children } = this.props;

        return(
            <RationContext.Provider value={{ ration, addRation }} >
                {children}
            </RationContext.Provider>
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们必须在某处列出所提供的配给量。让我们看看如何使用钩子来做这件事。
为此我们需要在`RationContext.js`
中更改一行

```
 const RationContext to export const RationContext 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在 RationList.js 中列出配给量

```
 import React,{ useContext } from 'react';
    import { RationContext } from './RationContext';

    export const RationList = () => {
        let { rations } = useContext(RationContext);

        return(
            <div>
                <ul>
                    {rations.map(ration => <li>{ration}</li>)}
                </ul>
            </div>
        )
    } 
```

Enter fullscreen mode Exit fullscreen mode

酷毙了。现在让我们从 AddRation.js
添加 ration

```
 import React,{ useContext, useState } from 'react';
    import { RationContext } from './RationContext';

    export const AddRation = () => {
        let { addRation } = useContext(RationContext);

        updateRation = (e) => { e.keyCode === 13 ? addRation(e.value) : '' }

        return(
            <div>
                <input type="text" placeholder="Ration name" onKeyDown={updateRation}/>
            </div>
        )
    } 
```

Enter fullscreen mode Exit fullscreen mode

背景不是魔法，它位于大树的顶端🕵🏼‍♂️，并让它的员工(消费者)为它工作。这里我们考虑 *App.js* 是 App 的入口。现在让我们来看看 App.js.

```
// App.js
import React from 'react';
import { RationProvider } from './RationContext';
import { RationList } from './RationList';
import { AddRation } from './AddRation';

const App = (props) => {
    return(
        <RationProvider>
            <RationList />
            <AddRation />
        </RationProvider>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我想你一定对 React 钩子有了很好的概述。但是仍然有更多的钩子需要覆盖。期待第二部分很快🤗。直到那时再见...编码快乐！