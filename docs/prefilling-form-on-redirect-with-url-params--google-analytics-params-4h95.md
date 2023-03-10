# 用 URL 参数和谷歌分析参数重定向预填充表单

> 原文：<https://dev.to/set808/prefilling-form-on-redirect-with-url-params--google-analytics-params-4h95>

你好。我真的希望你能帮我。我已经创建了一个登陆页面，其中有一个简单的“形式”，以获得潜在用户的电子邮件和密码。

```
<input id="email" type="email" name="email" placeholder="E-mail Address" style="width:100%; border: 1px solid #eaeaea">
<input id="password" type="password" name="password" placeholder="Password" style="width:100%; border: 1px solid #eaeaea">
<a onclick="redirect()" class="custom-link btn btn-sm startscreening-btn btn-default btn-icon-left btn-shadow" data-title="Sign Up Now" title="Sign Up Now" style="margin-top: 1rem">Create Account</a> 
```

**创建账户**按钮有一个`onclick`事件监听器，调用函数`redirect` :

```
 <script>
const redirect = () => {
  const email = document.getElementById("email").value;
  const password = document.getElementById("password").value;

  let param = '';

  if (email || password) {
    const tempParam = "email=" + encodeURIComponent(email) + "&password=" + encodeURIComponent(password);
    param = "?" + window.btoa(tempParam);
  }

  window.location = "https://app.naborly.com/signup" + param;
}

</script> 
```

在重定向时，编码在`param`中的值用于预填充注册表单。到目前为止，这段代码按预期工作。问题是，一旦我试图在重定向 URL 上添加`utm_source`和`utm_campaign`参数，我就会遇到一些问题。

*   如果我将`&utm_source=some_source&utm_campaign=some_campaign`追加到`window.location`赋值的末尾，那么注册表单中就会填充垃圾值

*如果我将`&utm_source=some_source&utm_campaign=some_campaign`附加到`tempParam`赋值的末尾，密码值将缩短为 4 个字符。

有没有办法预先填写表单，并在同一个链接上有 GA 活动参数，或者这是一个徒劳的努力？