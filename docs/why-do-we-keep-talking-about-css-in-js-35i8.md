# 为什么我们一直在谈论 CSS-in-JS？

> 原文：<https://dev.to/siddharthkp/why-do-we-keep-talking-about-css-in-js-35i8>

还记得你第一次写一些 html/css 吗？太棒了，对吧？

```
<div style="color: blue">Hello world</div> 
```

如此简单，如此纯粹。

然后，当然，高级开发人员告诉你不要这样写你的 css，把它放在一个单独的文件中。

```
<div class="blue-text">Hello world</div> 
```

```
/* style.css */
.blue-text {
  color: blue;
} 
```

随着您构建更多的元素和页面，您的`style.css`开始变长，所以您将它分成多个文件。

```
/* landing-page.css */
.heading {
  font-size: 64px;
}
button {
  background: orange;
} 
```

```
/* forms.css */
button {
  background: blue;
} 
```

很快，你意识到为一个元素设计的风格开始冲突并覆盖其他元素。您采用某种形式的名称间距来为这些样式创建一个*范围*。

也许事情就像这样简单:

```
<form>
  <input placeholder="email" />
  <button>Submit<button>
</form> 
```

```
/* form.css */
form button {
  background: blue;
} 
```

或者更高级的比如 [BEM](http://getbem.com) :

```
<form class="form">
  <input class="form__input" placeholder="email" />
  <button class="form__button">Submit<button>
</form> 
```

```
/* form.css */
.form__button {
  background: blue;
} 
```

我真的很喜欢 BEM(以及其他基于约定的方法，如 OOCSS、ITCSS 等)。).你可以仅仅通过在你的团队中采用一个共同的惯例来解决一个棘手的问题。

无论哪种方式，您在这里解决的问题都是将样式限定在特定的上下文中。

同样的问题和解决方案也被带到了 React land。以这个`LoginForm`为例:

```
function LoginForm() {
  return (
    <form className="form">
      <input placeholder="username" type="text " />
      <input placeholder="password" type="password" />
      <button className="button">Submit</button>
    </form>
  )
} 
```

我们不知道这个按钮是否会与应用程序中其他地方的另一个按钮发生冲突。我们应该在这里使用某种范围。您仍然可以在 React 中使用 BEM 这样的命名空间。

```
<button className="form__button">Submit</button> 
```

这就是故事有趣的地方。在 React 组件中，我们不再编写普通的`HTML`，而是编写`JSX`。

上面一行 JSX 代码在编译时被转换成这段 javascript 代码:

```
React.createElement(
  'button',
  { className: 'form__button' },
  'Submit'
) 
```

现在，您已经掌握了编程语言(javascript)的全部能力。你可以做纯 CSS 做不到的事情。

### CSS-in-JS 的承诺

您可以将创建范围或名称间距的工作委托给该语言，而不是手动完成。

CSS 模块是 css-in-js 生态系统的门户。

这是你写的:

```
/* form.css */

button {
  /* look ma, no name spacing */
  background: blue;
} 
```

```
import styles from './form.css'

function LoginForm() {
  return (
    <form>
      <button className={styles.button}>Submit</button>
    </form>
  )
} 
```

这就是`styles.button` get 的编译结果:

```
function LoginForm() {
  return (
    <form>
      <button className="form__button__abc1">Submit</button>
    </form>
  )
} 
```

这与您手写的内容非常相似，但是它将您从避免冲突的责任中解放出来。我发现能够像在本地范围内一样编写自己的风格是一种难以置信的解放。

### CSS-in-JS 库的下一波浪潮

我们能够在工具/自动化部分利用这种语言的力量，我们能把它带到写作风格中吗？

这就是它成为争议的地方。每一个 CSS-in-JS 都采用稍微不同的方法，通过折衷来实现某个新特性。

例如:`jsxstyle`让你在元素上编写看起来像经典内联样式的样式，但是通过 webpack 插件将它们提取到一个文件中。

```
<Block component="button" backgroundColor="blue" /> 
```

另一方面，`styled-components`允许您在 css 中混合运行时逻辑。这意味着你可以不用接触你的配置就开始使用它，但是你不能把样式提取出来。

```
const Button = styled.button`
  background: ${getBackground};
`

function getBackground(props) {
  if (props.appearance === 'primary') return 'blue'
  else if (props.appearance === 'disabled') return 'grey'
  else return 'white'
} 
```

`linaria`用它的`css`标签走了一条有趣的中间路线，你可以在组件旁边创建类，这些类在构建过程中用一个 babel 插件提取出来。这意味着您仍然可以使用 javascript，但是它不能像前面的例子那样依赖于运行时逻辑。

```
const button = css`
  background: ${colors.blue};
`

function MyComponent() {
  return <button className={button}>Click me</button>
} 
```

如您所见，所有这些库都带来了一些东西来交换其他东西。这就是为什么它这么有争议，你可以拿任何一个库，在里面找瑕疵。

自动化命名空间样式的工作，但需要一些设置，这可能被视为过度工程(我们已经有手动边界元工作，没有任何设置)

另一方面，它不需要任何 babel/webpack 设置，但是它需要库在运行时存在，这稍微增加了 javascript 包的大小。

您必须选择适合您项目的折衷方案。

有了 Auth0 中的[设计系统，我们选择了`styled-components`，因为它帮助我们基于一组底层令牌和设计模式创建灵活的组件。](https://github.com/auth0/cosmos)

上周，我不得不构建了一堆包含一些表单逻辑的页面，我真的很喜欢使用`css-modules`，因为它帮助我编写了全局和页面特定的样式，而无需采用像 BEM 这样的手动方法。

你可以使用我的朋友 [Michele Bertoli](https://twitter.com/michelebertoli) 创建的这个[对照表](https://github.com/MicheleBertoli/css-in-js)。

希望这对你的旅途有帮助

（同 suddenionosphericdisturbance）电离层的突然骚扰

* * *

[![newsletter](img/92e996eac08edab05705ed94cc61f7e7.png)](https://sid.studio/newsletter)