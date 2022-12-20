# 彻底清除我(和你)关于渲染道具的想法

> 原文：<https://dev.to/bidah/clear-my-mind-and-yours-about-render-props-once-and-for-all-13db>

*加勒特·西尔斯在 Unsplash 上拍摄的照片*

我在野外已经看到了很多渲染道具的解释，但是我还是没有弄清楚。有时候你需要介入，自己想办法解决。这是我对此的想法。希望它也能为你服务。

## 你渲染你的道具。这个属性是一个返回 jsx 的函数。

您的 render 函数是返回函数调用的函数，该函数被定义并作为一个属性传递，该属性又返回最终呈现的 jsx。

```
<Hello
  render={() => (
      <p>El barto was here</p>
  )}
/> 
class Hello extends Component {
  render() {
    return this.props.render();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过使用`children` prop 作为您的函数来抽象和简化这一点。`children`是你怎么回事儿。这一次，它被定义为一个函数，就像在它返回 jsx 之前一样。

```
<Hello>
  {() => <p>El barto was here</p>} </Hello> />

class Hello extends Component {
  render() {
    return this.props.children();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 你的渲染道具函数参数

它们来自你的组件。通过调用 render 方法中的函数。将数据作为参数传递给它，以便在呈现组件时使用。这和往常一样，但是因为定义本身并不在你的组件中，而是在你使用它的代码库中的任何地方，所以你可能会迷路。只是一个新的提醒，这很有帮助。

```
<Hello>
  {(msg) => <p>{msg}</p>} </Hello> />

class Hello extends Component {
  render() {
    return this.props.children("el barto was here");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 你的组件道具

它们被传递给组件并照常使用。这里没什么新鲜的。

```
<Hello hide>
  {(msg) => <p>{msg}</p>} </Hello> />

class Hello extends Component {
  render() {
    return !this.props.hide ? this.props.children("el barto was here") : null
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 什么时候用？为了什么？

当你掌握了模式的工作原理，你就进入了下一个阶段“仍然没有 100%掌握”，也就是什么时候使用这个东西？我们快到了。我们知道这个东西是如何工作的，当使用实现它的库时，我们可以得到它。但是你也应该使用它！我们可以自己实现它，但我们不清楚何时实现。为此我们需要拼图的最后一块。

对于这一部分，我必须说我得到了感谢[贾里德·帕尔默](https://twitter.com/jaredpalmer)。通过这个视频，你可以实现一个小的 [Formik](https://github.com/jaredpalmer/formik) 实现，当完成时，它会给你一个清晰的模式有用性的概念。通过将一个没有渲染道具的组件转换成一个有渲染道具的组件，你就可以实现这个目标。

[https://www.youtube.com/embed/oiNtnehlaTo](https://www.youtube.com/embed/oiNtnehlaTo)

这里有一个配套的 codesandbox，是我做的，在你看的时候用来玩代码的。叉它，改变它，完成它。错误部分不见了。希望这能给你一个良好的开端，让你一劳永逸地真正理解它。祝你好运！🍀

[https://codesandbox.io/embed/5xr5vrjpxl](https://codesandbox.io/embed/5xr5vrjpxl)