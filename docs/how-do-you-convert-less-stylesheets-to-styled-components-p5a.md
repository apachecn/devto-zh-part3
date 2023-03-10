# 如何将较少的样式表转换成样式化的组件？

> 原文：<https://dev.to/codemochi/how-do-you-convert-less-stylesheets-to-styled-components-p5a>

Less 是一个流行的 CSS 语言扩展，它允许很多 CSS 梦寐以求的酷东西。“更少”的超能力来自于它带来的三个 CSS 没有的新能力——即选择器、变量和混合的嵌套。Less along 和 Sass 在 10 年前发明的时候彻底颠覆了 web 样式，因为它允许开发人员编写更接近他们编写 javascript 的样式。

那么，如果少是如此的好，为什么我要写一篇关于如何远离它的文章呢？React 最近获得了越来越多的动力，因为它能够将离散的组件组合在一起形成用户界面。

[![React components are building blocks.](img/b6d5eee1c96e14457843c2a4381ce29f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MId-CaKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/1a0929724a00340c65590e95562812d5/ae753/image-1.png)

React 的特点是可以将 HTML 和 javascript 一起编写，这样一个组件就可以将所有相关代码放在一起。Styled-components 是 Less 和 Sass 的替代方案，遵循同样的组织方式。现在，您可以为您的 javascript、html 和样式编写可组合的部分，并且您可以使用 javascript 函数来编写它们。真的是天下第一！

因此，如果我有旧的更少的代码，我如何把它转换成样式的组件？

第一步:为所有的全局样式表设置一个全局样式。

我们采用任何全局 css 样式，并通过用`createGlobalStyle`包装它来将其移动到全局样式中。

```
import { createGlobalStyle } from 'styled-components'

export const GlobalStyle = createGlobalStyle`
@font-face {
    font-family: "Roboto";
    font-weight: normal;
    src: local("Roboto"), url('https://fonts.google.com/...') format("truetype");
  }

  body {
      font-family: "Roboto", sans-serif;
      font-size: 12px;
      color: black;
  }

  h1 {
    font-size: 16px;
  }

  a {
      text-decoration: none;
      color: inherit;
  }

` 
```

然后，一旦我们定义了那个`<GlobalStyle />`组件，我们就把它添加到我们项目的根组件:

```
const Layout = ({ children, title }) => (
  <>
    <GlobalStyle />
    <Header />
    <Page />
    <Footer />
  </> ) 
```

第二步:将你所有的变量移动到一个主题风格中。

Styled-components 提供了一个全局主题组件，您可以在项目的根目录中定义它，并且它可以在任何地方访问。假设这些是我们希望在站点范围内访问的较少的变量:

```
@theme-text: #cccccc;
@label-theme: #2d5a6b;
@label-text: @theme-text;
@label-border: 1px solid @theme-text; 
```

我们创建了一个名为`theme`的 javascript 对象，我们可以在其中存储所有的常量。对象中的每个键将代表变量名，值将是它所在的值。

但是 javascript 对象的问题是，不能让一个值引用同一个对象中的不同值。因此，如果我有一个`color`值，我想创建第二个变量`backgroundColor`，它是`color`的反函数，当`color`的值与我设置它的位置在同一个对象中时，我*而不是*被允许引用它的值。

为了绕过这个限制，我们从将所有东西定义为单独的字符串开始。因为一个字符串可以使用另一个字符串的信息，所以我们没有任何问题。然后，一旦我们有了所有的字符串，我们就可以构建一个名为`theme`的巨型对象，并且我们已经准备好了。

首先，我们将所有较小的变量转换成字符串——在将破折号(-)转换成下划线(_)时要小心，因为在 javascript 变量名中不能有破折号。当我们创建主题对象时，我们*可以*在对象键名中有一个破折号，所以如果我们有一个名为`@theme-text`的变量，并希望在我们的样式化组件中坚持使用蛇格符号，我们可以通过在`theme`对象:`theme['theme-text']`中创建键`theme-text`。几行代码抵得上 1000 个字，所以这是我用的模式:

```
const color: '#442d6b';
const theme_text = '#cccccc';
const label_theme = '#2d5a6b';
const label_text = theme_text;
const label_border = `1px solid ${theme_text}`;

const theme = {
  color, //I don't need color: color here because the names are the same
  "theme-text": theme_text,
  "label-theme": label_theme,
  "label-text": label_text,
  "label-border": label_border
}

export default theme; 
```

我们现在必须将主题对象导入到项目根目录下的`<ThemeProvider>`对象中。只要确保我们导出了`theme`对象，然后将其导入到项目的根目录中，并与样式化组件库中的`<ThemeProvider>`组件一起使用:

```
import { ThemeProvider } from 'styled-components'
import myTheme from './theme' //This is the theme object that we defined above

const Layout = ({ children, title }) => (
  <>
    <ThemeProvider theme={myTheme}>
      <GlobalStyle />
      <Header />
      <Page />
      <Footer />
    </ThemeProvider>
  </> ) 
```

现在我们可以访问主题对象中的所有内容，作为所有样式化组件的参数，这些组件是`<ThemeProvider>`的子组件。我们将在*步骤 4* 中告诉你如何做到这一点，但是接下来我们必须告诉你如何复制其余的样式。

步骤 3:将较少的组件分解成可以附加到 react 组件的样式块。

因为使用 React 的一个主要好处是它的可组合性，所以我们现在必须将单个 less 样式表分解成离散的块。这并不像听起来那么糟糕，例如，如果我们有一个名为 BlogItem 的类，它有一堆没有样式化的项目卡，我们创建一个名为 blogItem 的样式化组件，并将所有样式复制到其中。您可以保留所有嵌套的样式和媒体查询。

```
.blogItem {
  font-size: 12px;
  margin-top: 24px;
  //..many more stylings excluded
} 
```

转到:

```
export const StyledBlogItem = styled.div`
  font-size: 12px;
  margin-top: 24px;
  //...many more stylings excluded
`

export const BlogItem = props => <StyledBlogItem props={props} /> 
```

**步骤 4:转换 less 样式表中引用的任何变量**

既然我们已经复制了大部分样式表代码，现在我们需要处理变量。因为这些变量现在已经在我们的主题中定义了，所以我们可以在任何样式的组件中通过使用主题参数来访问它们，如下所示:

```
import styled from 'styled-components'

export const BlogItem = styled.div` ${({ theme }) => `
      color: theme['color'];

    `} ` 
```

这里唯一的问题是，你需要注意一个较小的变量被一个数相乘或相除的地方。例如，将字体大小、边框半径或边框粗细乘以一个常数。在下面的例子中，我们有一个名为`global-border-radius`的变量，我们将其定义为 12px。通过在样式表的另一个地方用一个数字除它，可以得到一个更小的边界半径。这里，blogItem 类的边界半径为 6px，尽管全局设置为 12px。

```
@global-border-radius: 12px;

.blogItem {
  border-radius: @global-border-radius / 2;
} 
```

样式化的组件无法处理这种情况，因为您实际上是在尝试用数字`2`除字符串`12px`。这里有一个函数，你可以复制并使用它来自动处理这个问题:

```
export const modifySize = (fontString, modifyFrac) => {
  const fontNumber = parseFloat(fontString)
  const fontUnit = fontString.replace(/[0-9]/g, '')
  return `${fontNumber * modifyFrac}${fontUnit}`
}

const BlogItem = styled.div` ${({theme})=> `
    border-radius: `${modifySize(theme['global-border-radius'], 2)}`
  `}
` 
```

我们的函数`modifySize`是将字符串`12px`分解成数字`12`和字符串`px`。它将根据您传入的因子来计算数字的大小，并返回一个大小正确的字符串。

**第五步:将 mixins 转换成函数**

Mixins 是一种制作一小段样式的方法，可以应用于许多项目。这里有一个 mixin，它将为一个标题设置各种参数:

```
.heading (@selector, @size, @color: @theme-title) {
  text-align: center;
  font-size: @size;
  font-weight: bold;
  color: @color;
} 
```

我们可以通过创建一个函数来捕捉这一本质，该函数将返回一个包含所有样式信息的字符串，并且样式化的组件会很高兴地将它添加到您想要的任何位置:

```
export const heading = (theme, size, color) => {
  const setColor = color ? color : theme['theme-title']
  //I can set a default color if color isn't passed in

  return `
    text-align: center;
    font-size: ${size};
    font-weight: bold;
    color: ${setColor};
`
} 
```

最酷的是，如果我们愿意，我们甚至可以传入变量来控制函数的输出。唯一的问题是，如果您需要访问全局变量，您需要将主题参数传递给函数——它不会像创建样式化组件时那样自动成为参数。我们可以这样调用`heading`函数:

```
const BlogHeading = styled.div` ${({ theme }) => ` ${heading(theme, '16px', 'black')} `} ` 
```

**第六步:使用&和&进行散兵样式**

对于大多数转换来说，前 4 个步骤应该足够了，但是偶尔你会有一些风格不会通过。有一次我遇到了这种情况，我有一个 css 样式表形成了我的基本样式，然后当我试图用我的样式化组件覆盖它时，基本样式一直存在。

```
:not(pre) > code[class*='language-'],
pre[class*='language-'] {
  background-color: #fdf6e3;
} 
```

```
:not(pre) > code[class*='language-'] {
  font-family: 'Roboto-Mono';
  padding: 0.1em;
  border-radius: @border-radius-small;
  background-color: @post-highlight;
  color: @post-highlight-text;
} 
```

解决方法是在持续的样式前添加一个`&&`。每个`&`将为您添加的每个`&`生成类名。

```
const StyledContent = styled.div` ${({ theme }) => `
        && :not(pre) > code[class*='language-'] {
            font-family: 'Roboto-Mono';
            padding: 0.1em;
            border-radius: ${theme['border-radius-small']};
            background-color: ${theme['post-highlight']};
            color: ${theme['post-highlight-text']};
            background-color: purple;
        }
    `} ` 
```

从那里来的还有更多！
[点击这里给我们您的电子邮件，我们会在发布新内容时通知您。](https://gmail.us20.list-manage.com/subscribe?u=37f38485b2c7cff2f3d9935b5&id=e3bc056dde)我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时退订。

最初发布于 [CodeMochi](https://www.codemochi.com/blog/less-to-styled-components/) 。查看那里的新博客帖子。