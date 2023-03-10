# 键盘用户可访问的表单输入

> 原文：<https://dev.to/ananyaneogi/accessible-form-inputs-for-keyboard-users-1ai6>

我们都犯了做`outline:none`的错误，没有想太多，去掉了那些*难看的*蓝色轮廓的表单控件元素，比如按钮和输入字段。但是如果我们考虑让我们的网站更容易访问，我们肯定应该这样做，我们在任何一个可访问性清单中读到的第一个建议就是永远不要做`outline:none`，因为这样键盘用户几乎不可能浏览我们的网站。

那么我们如何克服这个问题呢？
简单。给`outline`一些合适的样式，比如`outline: 2px solid your_choice_of_colour`，你就不会再得到默认的轮廓样式了！

但是如果我们愿意采取额外的步骤，首先识别(或者至少尝试)键盘用户，并且只向他们展示那些轮廓(有风格的或者没有风格的),其余的可以继续他们的生活，而从来不知道他们的轮廓！

我们可以这样做-

首先确定用户是否点击了`tab`键，以及他们是否给你的`body`添加了一个类。

```
function handleTab(e) {
    if (e.keyCode === 9) { // "hey I am a keyboard user"
        document.body.classList.add('user-tabbing');
        window.removeEventListener('keydown', handleTab);
    }
}

window.addEventListener('keydown', handleTab); 
```

其次，添加您的特定风格，瞧！

```
body button:focus,
body input:focus,
body select:focus,
body textarea:focus {
    outline: 2px solid yellow;
}

body:not(.user-tabbing) button:focus,
body:not(.user-tabbing) input:focus,
body:not(.user-tabbing) select:focus,
body:not(.user-tabbing) textarea:focus {
    outline: none
} 
```

你有什么让网络对所有人更容易访问的建议？