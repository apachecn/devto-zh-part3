# 3 对初级开发人员的面试问题做出反应

> 原文：<https://dev.to/hliutongco/3-react-interview-questions-for-junior-devs-chn>

我向一些朋友询问了他们在找工作时遇到的棘手问题，这里有几个突出的问题。(请注意，这些都是针对入门级工作的问题，所以更高级的开发人员可能会发现这些问题不太难。)在滚动查看底部的解决方案之前，请花一分钟时间自己猜测答案！

### 1)在类组件内部，如果 state 在构造函数外部和构造函数内部都被初始化，哪个 state 对象会覆盖另一个？

例如:

```
class Example extends Component {
   constructor(props){
      super(props)

      this.state = {
         location: "inside constructor"
      }
   }

   state = {
      location: "outside constructor"
   }
} 
```

如果您要在 render 中显示 console.log 状态，location 的值是“内部构造函数”还是“外部构造函数”？

### 2)创建一个功能组件时，为什么我们还需要导入 React 库？

```
import React from 'react'

const Example = (props) => {
   return (
      <div>Example</div>
   )
} 
```

与类组件不同，功能组件不从 React 扩展组件类。那么我们为什么还要导入 React 库呢？

### 3)给定以下示例，console.logs 将以什么顺序运行？

```
class One extends Component {
   componentDidMount(){
      console.log("One is mounted")
   }

   render(){
      <Two />
      <Three />
   }
}

class Two extends Component {
   componentDidMount(){
      console.log("Two is mounted")
   }
}

class Three extends Component {
   componentDidMount(){
      console.log("Three is mounted")
   }
} 
```

花一分钟猜猜答案！当你准备好了，向下滚动。

## 解！

### 1)哪个状态对象会覆盖另一个？

**答:**在构造函数内部初始化的状态会覆盖在构造函数外部初始化的状态。这是因为，就代码如何编译而言，类将首先*初始化所有变量，然后*然后*运行构造函数生命周期方法。*

 *所以关于这个例子:

```
class Example extends Component {
   constructor(props){
      super(props)

      this.state = {
         location: "inside constructor"
      }
   }

   state = {
      location: "outside constructor"
   }
} 
```

在 render 内部，如果我们使用 console.log 来检查我们的状态，我们会看到“内部构造函数”作为 location 的值。

### 2)创建一个功能组件时，为什么我们还需要导入 React 库？

**答:**因为我们需要 React 库来解释功能组件返回的 JSX。毕竟，JSX 是在 React DOM 中创建对象的旧方法的语法糖，即 React.createElement()。所以即使我们没有扩展组件类，我们仍然需要 React 库来在网页上创建元素。

### 3)console . logs 将以什么顺序运行？

**回答:**日志将按以下顺序运行:

```
"Two is mounted"
"Three is mounted"
"One is mounted" 
```

换句话说，父组件是最后一个完成安装的组件。这是因为子组件需要在父组件被认为完全挂载之前挂载。

如果我们记下组件开始安装的时间和完成安装的时间，看起来应该是这样的:

```
** One begins mounting **

** Two begins mounting **
// Two finished mounting

** Three begins mounting **
// Three finished mounting

// One finished mounting 
```

就是这样！如果你遇到了不在这个列表中的棘手的 React 问题，请在评论中提出来。*