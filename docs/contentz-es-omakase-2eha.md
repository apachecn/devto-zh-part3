# 内容是 omakase

> 原文：<https://dev.to/sergiodxa/contentz-es-omakase-2eha>

https://sdx.im/articles/contentz-es-omakaseT3

**【omakase】**是我在铁路学说中第一次读到的一个词，是一个日语词，意思是*让你自己决定*，通常在寿司餐馆使用，在寿司师傅让他选择自己喜欢的食物和自己创造的东西比较好。

Rails 被认为是 omakase，因为在使用它时，开发人员留下了关于使用框架的决策(很多，而不是在他们创建的每个项目中做出这些决策。

[【content】](https://contentz.tech)、[【我的静态站点生成器】](https://sergiodxa.com/articles/presentando-contentz)是 omakase 作为轨道。使用 contentz 时，您会认为重要的是创建内容，这是您的网站或博客所独有的，并让后者做出不太重要的决定。

会出现吗？什么设计？是否将对站点进行优化？创建页面？此类物品？静止的？所有这些决定都是由一个人做出的。

这使您能够专注于最重要的事情，编写构成您站点的内容、文章或固定页面。

## 如果我想个性化我的网站怎么办？

生活中的一切都是关于传统的，快乐使你更容易写作和拥有一个好的地方，作为交换，你不能决定这个地方是什么样子或者一些事情是如何工作的。有很多很好的选项可以进行更多的自定义，如盖茨比、Next.js、Jekyll、Hugo 等。虽然今后您可以选择支持主题来自定义设计，但您的想法始终是保持原样。

什么最好保持原样？根据我的经验，看着别人从头开始创建自己的网站或博客，你花更多的时间创建网站和定制设计，最后当一切都准备好开始写作时，灵感或欲望已经消失了。这就是为什么像 Medium 这样的服务如此受欢迎，他们强迫你只写而不去想别的。

contentz 为您提供了这一功能，但您可以使用自己的域将站点托管在任何服务器上。

## 满意地开始吗？

instala Contentz desde NPM usando

```
yarn add contentz 
```

Enter fullscreen mode Exit fullscreen mode

然后用
创造你的第一篇文章

```
contentz write hola-mundo.mdx 
```

Enter fullscreen mode Exit fullscreen mode

将`published`改为`true`文章开头，现在使用
生成社交网络(Open Graph)图像

```
contentz social articles/hola-mundo.mdx 
```

Enter fullscreen mode Exit fullscreen mode

最后用
建造你的地盘

```
contentz build 
```

Enter fullscreen mode Exit fullscreen mode

您的站点将在文件夹“`public`”中生成，上传到 Netlify 或 GitHub Pages 等服务器，您的站点将准备就绪并运行。为了配置更多的东西，可以[阅读文档](https://contentz.tech/docs/)。