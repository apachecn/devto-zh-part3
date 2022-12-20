# JavaScript 快速提示:从文件夹导入

> 原文：<https://dev.to/sunnysingh/javascript-quick-tip-import-from-folder-3pd0>

> **注**:本文原写于[我的个人博客](https://sunnysingh.io/blog/javascript-import-from-folder)。我在这里为开发者社区重新发布它。

维护中型到大型项目是一件痛苦的事情。即使您试图将东西分成多个文件，您也会开始意识到管理导入变得越来越难。如果我说我的文件从来不是这样的，那我是在撒谎

```
// Holy imports Batman! 😲
import Grid from './components/Grid';
import Title from './components/Title';
import Text from './components/Text';
import Button from './components/Button';
import Alert from './components/Alert'; 
```

Enter fullscreen mode Exit fullscreen mode

因为我们从同一个文件夹中导入这些组件，这样做不是很好吗？

```
// Much better 😌
import { Grid, Title, Text, Button, Alert } from './components'; 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，[导入语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)不允许导入文件夹。然而，我想出了一个无论如何都允许这样做的解决方案。拿好你的钱，让我先告诉你怎么做。

## 指数转口模式

我不确定这是否有一个合适的名称，所以我称之为“索引再导出”模式。这是因为我们将创建一个`index.js`文件，其中我们文件夹中的所有文件都将被再次导入和导出。

以我们的`components`文件夹为例，让我们在`components/index.js`中创建一个文件。

接下来，我们将使用[导出语句](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export)的一个不常见的特性来从另一个文件
中导出

```
export { default as Grid } from './Grid';
export { default as Title } from './Title';
export { default as Text } from './Text';
export { default as Button } from './Button';
export { default as Alert } from './Alert'; 
```

Enter fullscreen mode Exit fullscreen mode

这不仅允许您从`components`文件夹中导入所有组件，而且还创建了本质上属于您自己的 UI 组件库。

这样做还有一个巨大的好处，那就是能够确切地看到您能够导入什么，尤其是如果您的导出并不都是默认导出的话。例如，假设在`components/Button.js`中有一个默认的和命名的导出:

```
export default Button; // default export of Button component
export BigButton; // named export of a Button variant 
```

Enter fullscreen mode Exit fullscreen mode

所以现在在`components/index.js`里面，我们可以加上`BigButton` :

```
export { default as Button, BigButton } from './Button'; 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。

## 权力越大，责任越大

有一种东西叫做对你的项目进行过度归档，所以在决定对每个文件夹都使用这种模式之前，我会小心谨慎。虽然导入多个组件很方便，但是当导入像实用程序/助手函数这样的小东西时，可能会变得很烦人。在编码的时候你会知道什么时候痛点是如此之高，以至于你会去触及它，但否则我不会过早地这样做。