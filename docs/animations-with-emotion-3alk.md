# 带情感库的动画

> 原文：<https://dev.to/paulryan7/animations-with-emotion-3alk>

嘿，伙计们，这是一篇很短的文章，只是向你们展示如果你用情感来设计你的组件，你如何添加动画。

最终结果在这里(这是我创建第一个情感教程时用的沙盒):

[https://codesandbox.io/embed/ovm116xyj5](https://codesandbox.io/embed/ovm116xyj5)

你需要做的第一件事是导入`keyframes` :

```
import { keyframes } from "@emotion/core"; 
```

接下来我们要做的是创建我们的动画，让我们创建一个弹跳效果:

```
const Hop = keyframes`
 from, 20%, 53%, 80%, to {
    transform: translate3d(0,0,0);
  }

  40%, 43% {
    transform: translate3d(0, -30px, 0);
  }

  70% {
    transform: translate3d(0, -15px, 0);
  }

  90% {
    transform: translate3d(0,-4px,0);
  }
`; 
```

这类似于你如何用情感来设计组件，但是我们不用`styled`，而是用`keyframes`。

我们现在需要创建一个样式化的组件来使用这个动画，让我们把这个动画放在一些文本周围，命名如下:

```
const Text = styled("text")`
  animation: ${Hop} 1s linear infinite;
`; 
```

天啊，我喜欢模板文字！

还有一步，那就是换入我们的`Text`常量，这基本上意味着用`Text`替换文本周围的`div`，就像这样:

```
<Text className="country">
  <span>Iceland</span> </Text> 
```

神奇的是，我们的文本现在反弹了，这有多简单？现在开始制作一些:D 的动画吧