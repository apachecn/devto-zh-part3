# 使用没有表单的 HTML 表单验证(有点)

> 原文：<https://dev.to/raymondcamden/using-html-form-validation-without-a-form-kinda-aip>

这将是一个快速的。一段时间以来，我一直是[基于 HTML 的表单验证](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms/Form_validation)的超级粉丝。尽管它远非完美(并且必须总是与服务器端验证相结合)，但我喜欢它可以在提交过程的早期捕捉错误并为用户创造更好的体验。我对服务器端编程的第一次体验是编写 Perl 脚本来处理表单，所以任何能改进这个过程的东西对我来说都非常重要。

当我想到我想写的另一个演示时(我非常希望我在 Trello 中写下了它，因为我现在对此一无所知)，我意识到我需要验证一些电子邮件地址。虽然我对一个“不完美”的解决方案没有意见，但我很好奇，在使用
时，是否有一些方法可以绑定到浏览器的电子邮件验证

```
<input type="email" name="forUsToSpamYou" required /> 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我想要与字段提供的完全相同的验证，但是不使用用户输入和真正的表单。事实证明可以，而且相当简单，但是仍然需要使用一个表单。

先加了一个字段，然后用 CSS 隐藏，因为 CSS 就是那样牛逼:

```
<input type="email" id="testEmail">

<style>
#testEmail {
  display: none;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

然后我创建了一组数据。这是硬编码的，但是想象一下它来自其他进程。

```
let tests = [
  "foo@foo.com",
  "foo",
  "goo@goo.com",
  "zoo"
]; 
```

Enter fullscreen mode Exit fullscreen mode

然后为了测试这些值，我得到了一个对字段的引用，设置了值，并对它运行了`checkValiditity`:

```
let emailField = document.querySelector("#testEmail");

tests.forEach(t => {
  emailField.value = t;
  console.log(t, emailField.checkValidity());
}); 
```

Enter fullscreen mode Exit fullscreen mode

根据 MDN，`checkValidity`这样做:“如果元素的值没有有效性问题，则返回 true 否则为假。如果元素无效，此方法还会在元素处导致无效事件。

下面是修改后的结果，将结果写入一个 div 标记:

[https://codepen.io/cfjedimaster/embed/ZNBjYJ?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/ZNBjYJ?height=600&default-tab=result&embed-version=2)

需要澄清的是，这是*而不是*完美的电子邮件验证。每次我写关于这个话题的博客，人们都会指出 500 个打破这个话题的边缘案例。同样，我只是在寻找对输入进行更多“软”验证的东西。正如我所说的，我很好奇是否可以“链接”到 HTML 逻辑中，而不使用真正的(可见的)表单。有人在生产中用过这样的东西吗？请在评论中告诉我！

### 回合二！

昨晚写了这篇博文，实际上并没有在网上推广。我本来打算今天做的。但是在我发了帖子之后，all around smart guy[ime Vidas](https://twitter.com/simevidas)在下面的评论中发布了一个很棒的提示。我总是忘记可以用 JavaScript 创建 HTML 元素。他修改了我的代码，这样就没有*没有* HTML 表单字段，也不需要 CSS，你只需用 JavaScript 创建字段，如下:

```
let emailField = document.createElement('input');
emailField.type = 'email'; 
```

Enter fullscreen mode Exit fullscreen mode

这是他的密码笔:

[https://codepen.io/simevidas/embed/JqWWzo?height=600&default-tab=result&embed-version=2](https://codepen.io/simevidas/embed/JqWWzo?height=600&default-tab=result&embed-version=2)