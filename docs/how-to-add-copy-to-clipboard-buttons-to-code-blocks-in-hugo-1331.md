# 如何将复制到剪贴板按钮添加到 Hugo 中的代码块

> 原文：<https://dev.to/dguo/how-to-add-copy-to-clipboard-buttons-to-code-blocks-in-hugo-1331>

编程相关网站的一个小的生活质量改进是在代码块中添加复制到剪贴板按钮。当访问者想要复制代码示例或 shell 命令时，只需点击一个按钮，而不是手动选择文本、右键单击并按下 copy，这是非常好的。

我用[雨果](https://gohugo.io/)建立我的[个人网站](https://www.dannyguo.com/)。虽然 Hugo 内置了对[语法高亮](https://gohugo.io/content-management/syntax-highlighting/)的支持，但它不支持复制按钮。以下是我如何把这个功能添加到我的网站上。最终结果如下所示:

[![code block with a copy button](img/520a15037d09f11c281990dda17be020.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K2mh3wjv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8SBpdIT.png)

## 添加按钮

我检查了包含代码块的页面的源代码，发现 Hugo 生成的每个代码块都包含如下标记:

```
<div class="highlight">
    <pre>
        <code>...</code>
    </pre>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

没有语法突出显示的代码块具有相同的结构，但是周围没有突出显示的 div。考虑到这两种情况，我选择了作为 [pre](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/pre) 元素的子元素的代码元素。

```
document.querySelectorAll('pre > code').forEach(function (codeBlock) {
    var button = document.createElement('button');
    button.className = 'copy-code-button';
    button.type = 'button';
    button.innerText = 'Copy';

    var pre = codeBlock.parentNode;
    if (pre.parentNode.classList.contains('highlight')) {
        var highlight = pre.parentNode;
        highlight.parentNode.insertBefore(button, highlight);
    } else {
        pre.parentNode.insertBefore(button, pre);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

对于我见过的许多复制代码按钮的实现，按钮位于代码块的右上角或右下角。然而，我注意到，如果行太长，按钮会掩盖一些代码，特别是在移动设备上。为了避免这种可能性，我将每个按钮放在整个代码块之前。

有些实现只在用户悬停在代码块上时显示按钮，但是为了便于发现，我让按钮总是可见的。

为了设计按钮的样式，我使用了这个 CSS:

```
.copy-code-button {
    color: #272822;
    background-color: #FFF;
    border-color: #272822;
    border: 2px solid;
    border-radius: 3px 3px 0px 0px;

    /* right-align */
    display: block;
    margin-left: auto;
    margin-right: 0;

    margin-bottom: -2px;
    padding: 3px 8px;
    font-size: 0.8em;
}

.copy-code-button:hover {
    cursor: pointer;
    background-color: #F2F2F2;
}

.copy-code-button:focus {
    /* Avoid an ugly focus outline on click in Chrome,
       but darken the button for accessibility.
       See https://stackoverflow.com/a/25298082/1481479 */
    background-color: #E6E6E6;
    outline: 0;
}

.copy-code-button:active {
    background-color: #D9D9D9;
}

.highlight pre {
    /* Avoid pushing up the copy buttons. */
    margin: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 与剪贴板交互

接下来，我研究了如何使用 JavaScript 将[复制到剪贴板。这样做最流行的库是](https://stackoverflow.com/q/400212/1481479) [clipboard.js](https://clipboardjs.com/) ，但是如果可能的话，我想避免引入依赖。

一种方法是将 [execCommand](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand) 与`document.execCommand('copy')`一起使用，这将复制当前的文本选择。[在](https://github.com/zenorocha/clipboard.js)的引擎盖下，clipboard.js 使用了这个方法。

然而，有一种更新的方法，即[剪贴板 API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) 。它有几个优点:它是异步的，接受任意的文本/数据(因此它不必已经存在于页面上)，并且在处理权限方面有更好的故事。Chrome、Firefox 和 Opera [已经支持它](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API#Browser_compatibility)。对于其他浏览器，有一个[多填充](https://github.com/lgarron/clipboard-polyfill)。

我将代码放在一个函数中，并添加了点击处理程序。我使用 [innerText](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/innerText) 来获取要复制的代码。复制操作完成后，按钮会显示持续两秒钟的错误消息或成功消息。

```
function addCopyButtons(clipboard) {
    document.querySelectorAll('pre > code').forEach(function (codeBlock) {
        var button = document.createElement('button');
        button.className = 'copy-code-button';
        button.type = 'button';
        button.innerText = 'Copy';

        button.addEventListener('click', function () {
            clipboard.writeText(codeBlock.innerText).then(function () {
                /* Chrome doesn't seem to blur automatically,
                   leaving the button in a focused state. */
                button.blur();

                button.innerText = 'Copied!';

                setTimeout(function () {
                    button.innerText = 'Copy';
                }, 2000);
            }, function (error) {
                button.innerText = 'Error';
            });
        });

        var pre = codeBlock.parentNode;
        if (pre.parentNode.classList.contains('highlight')) {
            var highlight = pre.parentNode;
            highlight.parentNode.insertBefore(button, highlight);
        } else {
            pre.parentNode.insertBefore(button, pre);
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我添加了对浏览器是否支持剪贴板 API 的检查。否则，脚本从 [CDNJS](https://cdnjs.com/libraries/clipboard-polyfill) 加载聚合填充。

```
if (navigator && navigator.clipboard) {
    addCopyButtons(navigator.clipboard);
} else {
    var script = document.createElement('script');
    script.src = 'https://cdnjs.cloudflare.com/ajax/libs/clipboard-polyfill/2.7.0/clipboard-polyfill.promise.js';
    script.integrity = 'sha256-waClS2re9NUbXRsryKoof+F9qc1gjjIhc2eT7ZbIv94=';
    script.crossOrigin = 'anonymous';
    script.onload = function() {
        addCopyButtons(clipboard);
    };

    document.body.appendChild(script);
} 
```

Enter fullscreen mode Exit fullscreen mode

在剪贴板 API 变得无处不在之后，我将删除 polyfill 代码。

## 用 Hugo 智能加载

在我得到工作的功能后，我考虑如何包含脚本。我有三个选择。第一是不加区别地把它放在每一页上。这个脚本很小，但是为了优化，我想只在实际需要的时候包含它，这样可以节省一点带宽和一个网络请求(或者两个，如果需要 polyfill 的话)。

第二个选择是使用一个定制的 Hugo front matter 变量。使用这种方法，我会在每个有代码块的帖子上设置一个标志。然后模板可以检查这个标志。然而，这种方法涉及到手工操作，并且冒着我忘记做的风险。

第三个选择是找到一种方法，使用 Hugo 来确定哪些页面至少有一个代码块。正则表达式似乎是一条出路。我使用 Hugo 的 [findRE](https://gohugo.io/functions/findre/) 函数来确定 HTML 是否包含了一个`pre`元素。

```
{{ if (findRE "<pre" .Content 1) }}
    <script src="/js/copy-code-button.js"></script>
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

我给它传递了一个极限参数`1`，因为我只关心页面*是否有*代码块，而不是代码块的总数。

请记住，这个脚本应该在页面内容之后加载，最好是在正文的末尾，这样就不会阻碍呈现。否则，选择器可能会在代码块实际存在之前运行。

## 非 Hugo 网站

这个解决方案应该很容易为非 Hugo 网站工作。脚本中唯一特定于 Hugo 的部分是`pre > code`选择器。只需要修改选择器和按钮插入的位置。

## CodeCopy

[CodeCopy](https://github.com/zenorocha/codecopy) 是针对 [Chrome](https://chrome.google.com/webstore/detail/codecopy/fkbfebkcoelajmhanocgppanfoojcdmg) 和 [Firefox](https://addons.mozilla.org/en-US/firefox/addon/codecopy/) 的浏览器扩展，在很多可能有复制按钮的网站的代码块中添加了复制按钮，比如 [GitHub](https://github.com/) 和 [Stack Overflow](https://stackoverflow.com/) 。是 clipboard.js 后面的[同一个人](https://zenorocha.com/)做的。

*发现错误或错别字？请随时在 [GitHub](https://github.com/dguo/dannyguo.com/blob/master/content/blog/how-to-add-copy-to-clipboard-buttons-to-code-blocks-in-hugo.md) 上打开拉取请求。*