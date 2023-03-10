# CSS 变量突击测验细分

> 原文：<https://dev.to/terabytetiger/css-variables-pop-quiz-breakdown-2im9>

# [T1】简介](#intro)

你可能已经看到了最近流传的这条推文:

液体错误:内部

如果你还没有，我鼓励你在继续之前猜测你认为答案会是什么(如果投票仍然开放，甚至在推特上投票！-但请不要在这篇帖子后投票。在这里研究荣誉系统)。

*   粉红色
*   蓝色
*   红色

这是最后一次剧透警告！

* * *

# CSS 变量

如果您不熟悉 CSS 变量，这段代码可能会让您感到特别困惑。TL；DR 允许你给一个变量赋值，用`--`表示，然后在你的 CSS 样式表中引用这个值。这样做的好处是不需要更新代码的每个实例，而只需要更新一个位置。

```
/* Variables are often declared in the root pseudo-class
to ensure they are available across the entire style sheet */
:root {
  --paddingVertical: 5px;
  --paddingHorizontal: 10px;
  --margin: 15px;
  --textColor: black;
}

/* Referencing variables */
.example{
  padding: var(--paddingVertical);
  padding: var(--paddingHorizontal);
  color: var(--textColor);
} 
```

Enter fullscreen mode Exit fullscreen mode

Codepen 示例(在 divs 中添加了边框以示强调):
[https://codepen.io/Pichuplayer/embed/mYxxqN?height=600&default-tab=result&embed-version=2](https://codepen.io/Pichuplayer/embed/mYxxqN?height=600&default-tab=result&embed-version=2)

要了解更多细节，请查看 [MDN 文档中的内容](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)。

# 解

既然我们已经排除了 CSS 变量(并且足够低)，我们可以开始查看 tweet 代码中发生了什么(从这里开始称为“测验”)。

测验的代码:
[https://codepen.io/Pichuplayer/embed/jozzNW?height=600&default-tab=result&embed-version=2](https://codepen.io/Pichuplayer/embed/jozzNW?height=600&default-tab=result&embed-version=2)

所以问题的答案是**红色**。*但是为什么呢？*

测验中发生的是 CSS 变量`--myVar`被覆盖，最后一次覆盖生效。您可以通过复制 span 并使用`class='red'`将其添加到 div 之外来看到这一点:

[https://codepen.io/Pichuplayer/embed/PvRRXZ?height=600&default-tab=result&embed-version=2](https://codepen.io/Pichuplayer/embed/PvRRXZ?height=600&default-tab=result&embed-version=2)

现在我们看到有一个红色和蓝色的“Hello World”正在显示。这是因为蓝色的“Hello World”没有应用`--myVar: red`的类，因此使用它能找到的最近的赋值(设置在`#blue`)。

# 我对(当前)结果的观察

截至编写本报告时，民意调查显示如下(1，454 票):

*   粉色- 37%
*   蓝色- 18%
*   红色- 45%

**粉色**——我相信 CSS 变量的使用会让人犯错，并导致投票支持粉色。我们一遍又一遍地被告知 CSS 阅读器是从上到下，这可能导致相信因为`--myVar: pink`是最接近底部的，这将意味着粉红色是整个文档的值`myVar`。

**蓝色** -我对这个解决方案的最初解释是，这是 CSS 特异性的一个例子(重新评估后，我不确定这是否是正确的措辞)。我怀疑那些选择蓝色的人要么选择他们认为最不可能的答案，因为他们认为这是一个陷阱问题，要么评估 id 标签将优先于其他一切(至少，这是我过去的想法！)

你可以在 Emma 和 Muna 的文章中读到关于 CSS 特异性的内容:

## 文章不再可用

[![munamohamed94 image](img/20053ea493422eceeabb2abfe006d53f.png)](/munamohamed94) [## CSS 的特异性和用途！重要:案例研究

### 穆纳穆罕默德 10 月 14 日 188 分钟阅读

#webdev #css #beginners #tutorial](/munamohamed94/css-specificity-and-the-use-of-important-a-case-study--11)