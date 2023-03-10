# React 中默认属性值

> 原文：<https://dev.to/stephencweiss/default-prop-values-in-react-402j>

如果没有赋值，道具会得到什么值？

为了更具体地思考这个问题，想象你有一个更高阶的分量，`Container`。该组件*什么也不做*，只是呈现一个子组件`RandomComponent`，并向其传递一个属性`propInQuestion`。

( **NB** :在下面的代码片段中，我跳过了某些细节，比如`import`等等。放大相关信息。)

有几点需要注意:

1.  我们从不给`propInQuestion`赋值。
2.  `propInQuestion`不是从调用`Container`的地方传来的参数。

```
const Container = () => (
    <RandomComopnent propInQuestion />
) 
```

在这一点上，你可能期望`propInQuestion`是`undefined`。我做到了。

但是在我们结束之前，让我们看看我们的`RandomComponent`

```
class RandomComponent extends Component {

  render () {
    const {propInQuestion} = this.props;
    return (
      <React.Fragment>
      <p>`The value of propInQuestion? --> ${propInQuestion}`</p>
      </React.Fragment>
    )
  }
}

RandomComponent.defaultProps = {
    propInQuestion: false,
}; 
```

当我看到这个图案时，我首先注意到的是底部的`defaultProps`。**终于来了！**我被赋值了！所以答案是`false`，对吗？

不对。

原来一个道具传来传去，默认是`true`，镜像 HTML 的行为。也就是说，我们实际上通过`Container`为`propInQuestion`传递了一个值，尽管这是它第一次被引用。

所以，事实证明`Container`做的比我最初认为的要多。通过传递道具`propInQuestion`，它给了它一个值。同样的事情，一个更冗长的写法是:

```
const Container = () => (
    <RandomComopnent propInQuestion={true} />
) 
```

另一方面，为了让*使用*中的`defaultProps`，就需要:

```
const Container = () => (
    <RandomComopnent />
) 
```

你知道的越多！

感谢来自 [CodingBlocks 社区](https://codingblocks.slack.com)的@格兰克斯、[@赞美诗作者](https://dev.to/tehpsalmist)和 [@minimumviableperson](https://dev.to/minimumviableperson) 为我指明了正确的方向！