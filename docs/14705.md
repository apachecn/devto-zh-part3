# 3 HTML5 输入属性帮助移动用户

> 原文：<https://dev.to/deadlybyte/3-html5-input-attributes-to-help-mobile-users-1ofp>

# 你感觉到疼痛了吗？😫📱

*   当你输入第一个字母后，你的手机马上把第一个字母变成大写？
*   当你的手机认为单词拼写错误时会自动纠正？
*   当你的用户名被一条红色的波浪线标记为拼写错误时。

如果以上任何一项在过去让你感到沮丧，那么你需要成为一个好公民/开发者，并确保在你的项目中使用这些属性。

*   自动资本化
*   自动更正
*   拼写检查

**注意:**这些属性只适用于类型为*文本*、*电子邮件*或**文本区域**标签的**输入**标签。

## 自动资本化

该属性设置为 *none* 时，会阻止浏览器通过自动大写单词来帮助用户。

```
<input type="text" autocapitalize="none" /> 
```

## 自动更正

当该属性设置为 *off* 时，会阻止 iOS 自动更正文本框中输入的单词。通常，对于用户名字段，该属性是关闭的。即不在字典中并且不应该被自动纠正的随机字符串。

```
<input type="text" autocorrect="off" /> 
```

## 拼写检查

当这个属性被设置为 *false* 时，当一个单词被拼错并带有下划线时，浏览器将停止高亮显示。

```
<input type="text" spellcheck="false" /> 
```

## 把所有的东西放在一起

下面的示例说明了登录表单如何使用属性来简化移动用户的登录。

```
<form>
    <label for="username">Username</label>
    <input id="username" name="username" type="text" autocapitalize="none" autocorrect="false" spellcheck="false" />
    <label for="password">Password</label>
    <input id="password" name="password" type="password" />
    <button type="submit">Sign In</button>
</form> 
```