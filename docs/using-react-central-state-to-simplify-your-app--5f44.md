# 使用 React 中央状态简化您的应用程序

> 原文：<https://dev.to/greenstage/using-react-central-state-to-simplify-your-app--5f44>

React-central-state 是一个易于使用的 React 库，旨在帮助您在组件之间共享公共状态，而无需将状态传递给 props，并最大限度地减少重新渲染。它可以在 npm 和 github 上获得。

如果您不想处理 redux/mobx 的复杂性，或者您的应用程序还没有大到足以证明这些工具的价值，我建议一种不同的、更简单的方法:

```
 import React from 'react';
import {CSComponent} from 'react-central-state';

class _Component1 extends React.Component {
  ...
  changeFoo(value){
    this.setCentralState({foo:value});
  }
  ...
}

class _Component2 extends React.Component {
  ...
  updateWith(){
    return ['foo','someOtherProperty','anotherOne'];
  }

  render(){
    let foo = this.centralState.foo || "Nothing here";
    return <div>{foo}</div>
  }
}

export const Component1 = CSComponent(_Component1);
export const Component2 = CSComponent(_Component2); 
```

## 为什么用它

*   易于使用，几乎不需要额外的代码。

*   语法非常类似于普通的 react 状态管理。

*   它允许所有组件从一个中央状态读取和更新。

*   您可以将它与 react 的正常状态一起使用。

*   让您控制什么触发您的组件更新。

*   仅在需要时重新渲染。

### 状态变化时…

*   每个组件只订阅它感兴趣的中央状态属性更改。

*   组件由安装顺序通知。shouldComponentUpdate 方法将 nextCentralState 作为第三个参数注入，返回 false 将避免重新渲染，也就是默认行为。

## 工作原理

商店保存关于组件安装顺序的信息。当它的状态发生变化时，更新通过观察至少一个变化的属性的组件被有序地分派。

redux 通过每个组件属性映射相关的“子状态”来实现这一点，而 central state 要求您声明所有属性，当这些属性发生更改时，需要重新呈现。

这种有序的分派还避免了由所有者组件使子组件重新呈现或者回调中状态的改变所导致的重复呈现调用。

## 下行

*   访问 render()中未由 updateWith()返回的中央状态属性可能会导致“脏”DOM 元素，即，与应用程序状态不同步。

*   另一方面，从 updateWith()返回未在 render()中访问的属性键很可能会导致无用的重新呈现。
    最后你只需要注意 render 需要跟上什么属性就可以了。

## 如何使用

您只需要对代码做两处调整:

*   用 CSComponent 包装导出的组件

*   在 wrapped components 方法上实现 updateWith()，并返回一个中心状态属性数组，这些属性通知组件在更改值时进行更新。

### 从中央状态更新和读取:

很像在 vanilla react 中，使用 this.setCentralState()来更新和读取 this.centralState 属性。
你也可以注册当一个或多个属性改变时调用的函数，就在 react 组件被重新渲染之前:

```
import React from 'react';
import {CSComponent} from 'react-central-state';

class Display extends React.Component {
   constructor(props){
     super(props);

     //Register listener for 'foo' object changes
     this.fooListener = this.addCentralStateListener(prevstate=>{
       alert("Foo changing from "+prevstate.foo+" to "+this.centralState.foo);
     },'foo');
   }

   //Dont forget to remove the listener on unmounting
   componentWillUnmount(){
     this.removeCentralStateListener(this.fooListener);
   }

   updateWith(){return ['foo']};

   //Re-render occurs after this.fooListener is called.
   render(){
     return <div>{this.centralState.foo}</div>
   }
}

export default CSComponent(Display); 
```

## 下一步

*   当从同一个回调/事件处理程序完成更新时，对更新进行某种批处理。

就是这样，如果你觉得我在这里列出的好处大于坏处，那就去试试吧。

此外，我很高兴知道你的想法，如果你发现任何问题/错误，请在 [github](https://github.com/GreenStage/react-central-state) 上报告。