# 你不知道你需要的 5 个 CSS 技巧

> 原文：<https://dev.to/lynnewritescode/5-css-tips-you-didnt-know-you-needed-nb3>

在 web 开发机构工作多年后，我学到了一些技巧和诀窍。有些东西我每天都在用，甚至都没怎么想过。其中一些是相当标准的，其他的有点不寻常，但是下面所有的片段和例子都是我在客户网站上用过的。

### 转场

默认情况下，在我建立的所有网站上，我总是在链接和按钮上为它们的悬停状态添加 CSS 过渡。它只是在悬停时增加了一个不错的效果，并消除了当你与按钮或链接交互时的刺耳/突然的变化。

```
// Background colour transition
transition: background 0.2s ease-in-out; 
```

Enter fullscreen mode Exit fullscreen mode

对于一个按钮，我很可能会添加背景颜色的过渡，如上所示，对于一个链接，我会将过渡属性设置为 all ( `transition: all 0.2s ease-in-out`)，这可以允许我过渡悬停颜色和边框。

我也从来不用`text-decoration: underline`。你不能很好的控制使用它，但是你可以使用底部边框来获得更好的效果。填充允许更好的间距，如果你愿意，你甚至可以过渡或动画边框。

```
.link {
    transition: border 0.2s ease-in-out;
    border-bottom: 1px solid transparent;
}

.link:hover {
    border-color: blue;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 背景叠加

假设你有一些文本，完全放在图片的上面。但是背景图像太亮，使得文本不可读。你可以在那里添加另一个`div`在文本后面创建一个黑暗的覆盖，但是这不是很好，并且添加了一个额外的空的`div`，这并不是真正必要的。

伪元素来救援了！

使用`:before`元素意味着如果某个类被添加到`div`中，我们也可以应用它。下面是一个我如何实现这个叠加背景的例子:
[https://codepen.io/lynnewritescode/embed/aQRPNL?height=600&default-tab=result&embed-version=2](https://codepen.io/lynnewritescode/embed/aQRPNL?height=600&default-tab=result&embed-version=2)
上面代码的关键部分是设置你的叠加颜色和不透明度。这可以是任何颜色，我刚才以黑色和白色为例:

```
//Black overlay - 0.5 opacity
background: rgba(0,0,0,0.5);

//White overlay - 0.2 opacity
background: rgba(255,255,255,0.2); 
```

Enter fullscreen mode Exit fullscreen mode

### 多行下划线效果

下面这只笔可能对人们有用，也可能没用，但它是我在一个客户网站上被要求做的事情。它允许有一个大胆的下划线效果，将跨越整个句子的长度，即使它是多行的。
[https://codepen.io/lynnewritescode/embed/mwqBbX?height=600&default-tab=result&embed-version=2](https://codepen.io/lynnewritescode/embed/mwqBbX?height=600&default-tab=result&embed-version=2)
即使这不适用于你的设计，还是要求。它也可以用于链接悬停效果，如下所示:
[https://codepen.io/lynnewritescode/embed/zMmyJW?height=600&default-tab=result&embed-version=2](https://codepen.io/lynnewritescode/embed/zMmyJW?height=600&default-tab=result&embed-version=2)

### 粘性元素

需要一个元素贴在滚动条上，但不想使用 JavaScript，或者插件，或者你的内容高度是动态的，可能会改变？`position: sticky`是你的朋友。

```
position: -webkit-sticky;
position: sticky;
top: 0px; 
```

Enter fullscreen mode Exit fullscreen mode

当我不得不在一个手风琴元素旁边放一个工具条时，这对我来说非常方便。由于手风琴的开合，我不得不计算高度或使用其他复杂的方法，而`position: sticky`只是直接工作。我发现唯一需要注意的是，当 body 元素被设置为`overflow: hidden`时，它不起作用，并且在 IE11 中不受支持(它就是不粘，Edge 也没问题)。

### 防止高亮显示

能够在网站上选择文本是相当标准和期望的，但是我偶尔发现用户可以多次点击一个元素(例如一个旋转箭头),它选择/高亮显示该元素。

为了防止这种情况，您可以使用下面的代码片段:

```
.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
       -moz-user-select: none; /* Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome and Opera */
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你读到这里，感谢你的阅读！我希望这对一些人有用，如果有人感兴趣的话，我还希望能发布更多的日常片段。

关注我的 [twitter](http://twitter.com/lynnewritescode) 和 [instagram](http://instagram.com/lynnewritescode) ，了解更多与开发相关的内容！