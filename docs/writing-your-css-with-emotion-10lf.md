# 带着感情写你的 CSS

> 原文：<https://dev.to/bmcmahen/writing-your-css-with-emotion-10lf>

**【编辑】样式化组件也支持`css`道具！所以把这篇文章看做是一篇比较两种 api 的文章——`styled`API 和`css` api。**

我已经用几乎所有能想到的方式为 web 编写了 css。我使用过 css 模块、预处理程序，包括 Sass、Less 和 Stylus、BEM、函数式 css(使用 Tachyons)，也使用过许多 CSS-in-JS 库，包括 Styled-components 和 react-native 样式表。这些我都试过，使用 Emotion 的 css 道具无疑是我的最爱。我试着解释一下原因。

#### 情感 vs 风格化成分

让我们假设您已经加入了 CSS-in-JS 的行列。如果你不是，那么值得一读许多关于用 Javascript 编写 css 的好处的[伟大的](https://www.youtube.com/watch?v=R1_nGU0x3Wk) [文章]((https://mxstbr.com/thoughts/css-in-js/))和[视频](https://vimeo.com/116209150)。

我在这里的主要任务是论证使用[情感](https://emotion.sh)是为你的应用设计风格的最好的库。事实上，情感与 CSS-in-JS 领域中的其他 behomoth,[Styled-components](https://www.styled-components.com/)有很多相似之处。它甚至公开了一个基本上模仿样式化组件工作方式的`styled` api。但是在比较这两个库时，我实际上是在比较两种不同的组件样式。

一种是`styled-components`方式:

```
import styled from 'styled-components'

const Button = styled.button` 
  background: #08e; 
  color: white; 
  padding: 6px 10px; 
  border: none;
`

function Example() {
  return (
    <div>
      <Button>Hello world</Button>
    </div>
  )
} 
```

Styled-components 使用与模板文字相结合的`styled.button`样式 API 来创建带有样式的按钮。你可以看到它为什么受欢迎。有了这样的例子，这是一个漂亮的 API。

相比之下，使用 Emotion，我们可以使用`css`道具给元素添加样式，就像使用`style`道具一样。

```
/** @jsx jsx */
import { css, jsx } from '@emotion/core'

const Button = props => (
  <button
    css={{ 
      background: '#08e', 
      color: 'white', 
      padding: '6px 10px', 
      border: 'none', 
    }}
    {...props}
  />
)

function Example() {
  return (
    <div>
      <Button>Hello world</Button>
    </div>
  )
} 
```

乍一看，样式化组件的例子似乎更优雅。但是我发现从长远来看，我通常更喜欢`css`风格的 api，原因如下:

#### 你仍然在编写常规的 React 组件。

尤其是在使用 typescript 时，我认为这是有益的。您键入一个`Button`组件，就像您键入一个常规的 React 组件一样，允许您清楚地指定接受哪些属性。因此，您不太可能用奇怪的属性污染您的 dom 这是我在将自定义属性传递给样式化组件时发现的常见问题。

#### 对象样式更容易使用。

当使用 typescript 时，我喜欢我所有的 css 都经过类型检查并提供健壮的自动完成功能。我通常发现将主题变量插入对象比使用`${theme.color.red}`样式符号更容易。对象的一个小缺点是编写起来稍微麻烦一些，并且不容易从浏览器中复制。

#### 给事物命名很难。而且我很懒。

使用样式化组件时，通常需要为具有不同样式的组件创建名称。这导致许多组件缺乏明显的语义重要性，但仍然需要不同的名称。用一个有意义的描述符命名这些组件可能很困难。此外，在应用小的定制样式时，比如改变边距或填充，样板文件通常让人感觉很麻烦。所以，因为我很懒，我发现自己经常求助于使用`style`道具进行快速调整。

但是使用`css` prop 避免了这些缺陷，同时如果值得重用并且语义上有意义的话，仍然提供了将样式包装到组件中的机会。

```
function Example() {
  return (
    <div
      css={{
        margin: theme.spacing.sm,
        padding: theme.spacing.sm,
      }}
    >
      <Button variant="primary">Hi there</Button>
    </div>
  )
} 
```

#### 你把风格和元素放在一起。

有了`css`道具，所见即所得。这是一个小问题，但不必从一个元素滚动到另一个元素来寻找你的样式定义，这确实改善了我的工作流程。它感觉更有效率，并且让我在编写组件时保持流畅。需要删除一个元素？没有必要去寻找孤立样式的定义。

#### 作文容易死了。

考虑我们的`Button`组件。如果我们想给它提供一个利润呢？

```
function Example() {
  return (
    <>
      <Button css={{ marginRight: '1rem' }}>Cancel</Button> 
      <Button variant="primary">Save</Button>
    </>
  )
} 
```

这会将我们示例中定义的样式传递给`Button`元素，并应用自定义边距为我们编写样式。

#### 两者都很棒，但我选择情感

Styled-components 是一个很棒的工具，结合像 [styled-system](https://github.com/styled-system/styled-system) 这样的东西，你可以得到函数风格 css 的好处，它可以减轻一些命名问题。但是我发现使用`css`属性，特别是对于 typescript，减少了对 styled-system 之类的东西的需求，并且通常提供了用 Javascript 编写样式的最灵活的方法。

**建议链接**:

[Emotion.sh](https://emotion.sh/docs/introduction)

(这是一篇发表在我 benmcmahen.com 博客上的文章。点击这里[可以在线阅读。)](https://www.benmcmahen.com/why-i-choose-emotion/)