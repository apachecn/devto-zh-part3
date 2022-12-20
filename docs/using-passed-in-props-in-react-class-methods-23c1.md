# 在 react 类方法中使用传入的属性

> 原文：<https://dev.to/oyeyipowale/using-passed-in-props-in-react-class-methods-23c1>

利用从父组件传入或传下来的道具。您首先为该类创建了一个构造函数。检查下面的代码:

```
class SomeClass extends Component {
   Constructor(props) {
       super(props);
       this.props = props; 
   }

   someMethod = () => {
       console.log(this.props)
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

奇怪！以为会以其他方式发生。