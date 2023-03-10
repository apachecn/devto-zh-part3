# 如何将 web 字体导入带有样式组件的 React 应用程序 4

> 原文：<https://dev.to/alaskaa/how-to-import-a-web-font-into-your-react-app-with-styled-components-4-1dni>

# 厌倦了被限制在 CDN 上请求字体？不要再看了。

出于多种原因，您可能需要导入一种字体-从购买某些字体的公司许可证，到可用性问题，甚至是因为您最喜爱的字体无法通过 CDN(内容交付网络)获得。

自从我开始从事开发工作以来，我已经学会了如何直接导入 web 字体，而不是链接到可能托管字体的 CDN。由于我真的不知道从哪里开始，我想我应该写这个快速教程来告诉你如何在 React 设置中使用 styled components version 4 实现这一点——这是我最喜欢的在 React 应用程序中编写 CSS 的方式。

## 言归正传

首先，你需要选择你想要导入的字体，并下载 woff 和 woff2 格式。Woff(以及后来的 Woff2)代表 Web 开放字体格式，是万维网联盟(W3C)推荐的字体格式。它的特定格式压缩确保了它在所有浏览器中的性能，而且与从 CDN 请求字体相比，减少了网络字体延迟。

有几个网站可以下载这些内容，例如:

*   [https://www.onlinewebfonts.com](https://www.onlinewebfonts.com)

挑你喜欢的！我建议你至少导入这两种字体格式，woff 和 woff2，因为它们是 W3C 推荐的，并且在所有浏览器上都得到广泛支持。

在 React 项目中，在 src 文件夹中创建一个文件夹，并将其命名为“fonts”。在这个文件夹中，创建一个名为 fonts.js 的文件——这个文件将包含你的字体的导入，并在以后使它们对你的应用程序的其余部分可用。

在同一个文件夹中，现在放置你刚刚下载的两个字体文件。这将使您的应用程序可以访问它们以正确导入它们。这样做之后，你的文件结构应该看起来像这样:

```
src
|__fonts
    |__fonts.js
    |__nameOfYourFont.woff2
    |__nameOfYourFont.woff 
```

现在，让我们实际编写一些代码到 fonts.js 文件中，这样“样式组件”就可以将它们作为全局字体导入。

在 fonts.js 文件中，从“styled-components”导入{ createGlobalStyle }。这是一个方便的小助手，在你的应用程序中处理全局 css 样式。如果您想深入研究，请访问样式化组件文档上的 [createGlobalStyle。之后，您必须将字体导入到您的文件中，并在 font-face 声明中声明它们。](https://www.styled-components.com/docs/api#createglobalstyle) 

```
import { createGlobalStyle } from 'styled-components';

import NameOfYourFontWoff from './nameOfYourFont.woff';
import NameOfYourFontWoff2 from './nameOfYourFont.woff2';

export default createGlobalStyle`
    @font-face {
        font-family: 'Font Name';
        src: local('Font Name'), local('FontName'),
        url(${NameOfYourFontWoff2}) format('woff2'),
        url(${NameOfYourFontWoff}) format('woff');
        font-weight: 300;
        font-style: normal;
    }
`; 
```

这将导入您选择的 woff 和 woff2 格式的字体，并使其可通过字体文件访问。

但这只是战斗的一半。

虽然我们已经导入了特定的字体，但实际上我们还没有在应用程序中导入这些字体。

在 App.js 文件的顶部，导入 React 后，写

```
import GlobalFonts from './fonts/fonts';

// assuming you places fonts in your src folder as described above. 
```

在 App.js 文件中的某个地方，最好就在一个普通样式的组件下面，该组件通常包含站点布局或类似内容，并且不需要任何字体样式，将 GlobalFonts 组件放在 render 的返回中:

```
render() {
    return (
        <Wrapper> 
            <GlobalFonts />
    // ...
        </Wrapper>
    );
} 
```

这样做之后，你就可以在你选择的样式中的任何地方使用你的字体了。在任何样式组件文件中，只需声明(例如):

```
const AwesomeHeadline = styled.h1`
    font-family: 'Font Name';
`;

export default AwesomeHeadline; 
```

瞧，你刚刚导入了你的网络字体，并把它用在了标题中！

我希望这有助于你在将来导入字体，无论是工作还是有个人风格的个人项目！