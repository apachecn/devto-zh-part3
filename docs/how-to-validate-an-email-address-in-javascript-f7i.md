# 如何用 JavaScript 验证电子邮件地址

> 原文：<https://dev.to/tylermcginnis/how-to-validate-an-email-address-in-javascript-f7i>

电子邮件验证很难。如今存在大量复杂但有效的电子邮件地址，真正判断电子邮件地址是否有效的唯一方法是发送电子邮件并查看它是否被退回。也就是说，我们可以在前端做一些事情，让每个人的体验更好。在本帖中，我们将讨论几种在 JavaScript 中验证电子邮件地址的常用方法。

首先，当验证电子邮件地址时，我认为最好在允许的一方出错。我宁愿让几个假的电子邮件地址通过，也不愿拒绝一个有效的。前端电子邮件验证是要弄清楚语法是否正确，而不是电子邮件地址是否有效。也就是说，我们将从最宽松的解决方案开始，并朝着另一端努力。

```
function emailIsValid (email) {
  return /\S+@\S+\.\S+/.test(email)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您不熟悉 RegEx，`/\S+@\S+\.\S+/`正在测试最基本的电子邮件地址结构，`_@_._`。这是我对电子邮件地址的所有假设。如果用户输入的电子邮件不遵循这种结构，很可能是他们打错了。

上面正则表达式的一个问题是它会在这种格式下失败:`_@_@._`。为了解决这个问题，我们可以稍微修改一下正则表达式。

```
function emailIsValid (email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)
}

emailIsValid('tyler@tyler@tylermcginnis.com') // false
emailIsValid('tyler@tylermcginnis.com') // true 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。任何超出这一点的都太固执己见了。你可以通过谷歌搜索找到一些其他的解决方案，但是如果上面的例子对你不起作用，我建议你重新考虑你的方法。