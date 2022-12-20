# Wtf 是箭头函数？

> 原文：<https://dev.to/liltechnomancer/wtf-are-arrow-functions-529f>

我写这个帖子有点奇怪，因为我基本上只写箭头函数。我很少再使用传统的`function`语法了。然而，这篇文章仍然值得一写，因为我的许多学生只使用`function`语法，我想向他们展示另一种方式，并指出箭头函数的细微差别！

首先，我在说什么？嗯！很高兴你问了，让我给你看看下面。如你所见，我用两种不同的方式声明了两个非常简单的函数。最下面的是一个箭头函数。

Ooooh，真花哨。

一个**箭头函数**是一个使用`=>`语法声明的函数。明白了吗？它看起来像一个箭头。

```
function someFunc() {
  console.log('No Me Gusta')
}

const otherFunc = () => console.log('Me Gusta') 
```

Enter fullscreen mode Exit fullscreen mode

当使用像`map`这样的高阶函数时，它们真的很方便，你可以在这篇[博客文章](https://lvrbrtsn.com/blog/how-the-hell-do-i-use-map/)
中读到所有相关内容

```
someArray.map(function(item) {
  return item * 2
}) // Eww gross.

someArray.map(item => item * 2) // Sexy! 
```

Enter fullscreen mode Exit fullscreen mode

看到箭头函数看起来有多好了吗？我是简单性和最小语法的忠实粉丝。我相信这是因为我在编程的早期写了太多的 Ruby，而 arrow 函数在简单性上有很大贡献。

关于箭头函数，值得注意的第一件事是，当表达式在一行中时，`{}`括号是不必要的。正如你在上面所有例子中看到的。`return`也隐含了这一单行功能。多酷啊！

但这并不意味着每个箭头函数都必须是一行。如上所述，我总是使用箭头语法，这意味着我的多行函数看起来像这样。

```
const someNewFunction = (arg1, arg2) => {
  // Do some stuff with arg1
  // Do some other stuff with arg2
  // Solve world hunger
  return arg1 + arg2
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，当它是多行时，我们再次需要`return`。

在我结束这篇非常短的文章之前，另一件值得注意的事情是,`this`的上下文对于箭头函数是不同的。基本上，`this`在词汇上适用于箭头函数。意思是你认为`this`最初工作的方式只是为了学习你根本不知道`this`实际上是如何工作的。它保留了声明时的上下文，有点像任何面向对象语言中的 self。这意味着您可以在 React 中这样做，而不必担心绑定。

```
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = { count: 0 }
  }

  increase = () => this.setState({ count: this.state.count + 1 })
  render() {
    return (
      <div className="App">
        <button onClick={this.increase} />
        <h2>{this.state.count}</h2>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode