# 一些 CSS 技巧

> 原文：<https://dev.to/kwmcminn/a-few-css-tricks-okm>

如果你和我一样，在编程时设计你的应用程序可能会有点上瘾。你努力让你的网页看起来漂亮，会花太多时间在非常小的细节上。通过大量的 CSS 谷歌搜索，我发现了一些鲜为人知的 CSS 技巧，它们可能会让你的页面从平淡无奇变得漂亮！在这个博客中，我将展示一些我最喜欢的。如果你知道任何其他很酷的 CSS 技巧，欢迎在下面评论，我会添加到列表中。

### Fonts

一个好的字体可以改变你的页面，为你的应用程序增加个性和活力。幸运的是，CSS 允许您轻松地将字体导入到项目中。 [Google Fonts](https://fonts.google.com/) 是最好的字体资源之一，可以让你轻松地将自己喜欢的字体注入到项目中。只需将 URL 添加到 HTML 文件中，并在 CSS 中指定要继承字体的元素。【T2[https://codepen.io/kevin-mcminn/embed/gyepdG?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/gyepdG?height=600&default-tab=css,result&embed-version=2)

### 光标

我最近完成了一个项目，我需要一个不同的光标风格，但只在我的网页的特定区域。我了解到这是 CSS 中的一个光标选项，您可以在其中指定一个 div 或其他选择器来应用光标。下面你可以看到光标在文本上移动时的变化。

[https://codepen.io/kevin-mcminn/embed/zXWxLL?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/zXWxLL?height=600&default-tab=css,result&embed-version=2)

### 淡入/淡出

CSS 中的动画非常强大。如果您需要让文本或其他元素淡入或淡出页面，可以很容易地在 CSS 文件中添加一个 fade 属性。你所需要做的就是指定开始和结束的不透明度，以及你希望动画持续多长时间。
[https://codepen.io/kevin-mcminn/embed/gyebEr?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/gyebEr?height=600&default-tab=css,result&embed-version=2)

### 变换

另一个很酷很有用的动画是 *Transform* ，它允许你在页面上移动一个元素。下面我给图像添加了一个旋转属性，并指定在 4 秒内旋转 4 次。您可以使用 transform 属性制作所有类型的动画，例如缩放、倾斜、平移(水平/垂直移动元素)，甚至添加 3d 透视。【T2[https://codepen.io/kevin-mcminn/embed/OGvPeb?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/OGvPeb?height=600&default-tab=css,result&embed-version=2)

### 列

如果您的网页包含大量文本，您可能会想到在网页中添加分栏或分色。幸运的是，这可以通过 CSS 中的*列*属性轻松实现。下面我把我的段落分成了 4 列，同时也指定了边框的粗细来可视地分隔文本。

[https://codepen.io/kevin-mcminn/embed/jRzPPY?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/jRzPPY?height=600&default-tab=css,result&embed-version=2)

### 图像滤镜

现在你，是的，你，可以建立 Instagram！嗯，可能会涉及一些额外的编程知识，但过滤器是 instagram 的一个重要组成部分。幸运的是，CSS 滤镜属性给了你很多控制图片外观的方法。下面是一些你可以添加到图片中的滤镜。

[https://codepen.io/kevin-mcminn/embed/axYOwe?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/axYOwe?height=600&default-tab=css,result&embed-version=2)T3】

### 小型大写

这个 CSS 技巧可能不经常有用，但是当你想美化你的文本时，这可能就是答案。有了字体变体“small-caps ”,你可以将枯燥的文字变成优雅的字符串。
[https://codepen.io/kevin-mcminn/embed/BErNmm?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/BErNmm?height=600&default-tab=css,result&embed-version=2)

### 字母间距

再来一个动画！我认为这很有趣，你可以定义字母之间的空间，并使用动画来产生单词组合在一起的效果。这确保了一个戏剧性的入口！
[https://codepen.io/kevin-mcminn/embed/KYopRG?height=600&default-tab=css,result&embed-version=2](https://codepen.io/kevin-mcminn/embed/KYopRG?height=600&default-tab=css,result&embed-version=2)

*来源 [CSS 技巧](https://css-tricks.com/almanac/properties/t/transform/)