# 带反应钩的汉堡动画

> 原文：<https://dev.to/paulryan7/burger-animation-with-react-hooks-4n6e>

我最近在 Codesandbox 中使用 react 应用程序，想要制作一个汉堡 SVG 动画，但为了做到这一点，我需要使用 refs，这意味着我必须将我的功能组件更改为一个类！我还需要使用生命周期方法`componentDidMount`,所以看起来我已经别无选择了。

但是...可能...每个人都在谈论的钩子能救我吗？事实证明确实如此。

以前，我必须在构造函数中创建我的引用，如下所示:

```
constructor(props){
  super(props)
  this.burgerRef = React.createRef()  
} 
```

现在我能做的:

```
const burgerRef = useRef(null); 
```

我仍然需要一些方法来检测我的组件何时安装，否则我会试图动画一个未定义的元素，这是不好玩的！`useEffect`前来救援:

所以与其:

```
componentDidMount(){
    ....
} 
```

我只能做:

```
useEffect(() => {
  ....
}) 
```

瞧啊。我现在可以做一个漂亮的动画，而不需要改变我的组件成为一个类。

我在这里简单地讨论了理论，所有的代码包括动画都在下面的 Codesandbox 上(Codesandbox 真的很棒，我是一个 patreon，我认为每个人都应该这样，我没有因为这样说或推广 Codesandbox 而得到任何东西，但我就是喜欢它！！！！)

[https://codesandbox.io/embed/xrr2pyjkw](https://codesandbox.io/embed/xrr2pyjkw)