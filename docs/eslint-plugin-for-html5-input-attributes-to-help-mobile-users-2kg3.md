# 用于 HTML5 输入属性以帮助移动用户的 ESLint 插件

> 原文：<https://dev.to/deadlybyte/eslint-plugin-for-html5-input-attributes-to-help-mobile-users-2kg3>

最近我在 dev.to 上写了一篇文章，叫做 [3 HTML5 输入属性来帮助移动用户](https://dev.to/deadlybyte/3-html5-input-attributes-to-help-mobile-users-1ofp)😫📱。在那篇文章中，我强调了**自动大写**、**自动更正**和**拼写检查**属性，这些属性可以在移动用户填写表单时被禁用。

本文着眼于如何通过林挺自动化这些规则。我为 React 创建了一个 ESLint 插件，它会过滤你的代码，并警告你什么时候应该应用这些属性。规则会查看您的 JSX，如果它看到输入元素的类型、名称或 id 设置为**“email”**，那么它会检查这些规则。同样，当输入元素的名称或 id 设置为**“用户名”**时，它也会应用这些规则。

*   [eslint-plugin-jsx-form](https://www.npmjs.com/package/eslint-plugin-jsx-form) -这个插件可以在 NPM 上查看
*   [使用 Plugin - VSCode 安装&指南](#how-to-use)

# 规则📜

这是 ESLint 插件上的规则列表。

*   [电子邮件-禁用-自动资本化](#emaildisableautocapitalize)
*   [电子邮件-禁用-拼写检查](#emaildisablespellcheck)
*   [用户名-禁用-自动资本化](#usernamedisableautocapitalize)
*   [用户名-禁用-自动更正](#usernamedisableautocorrect)
*   [用户名-禁用-拼写检查](#usernamedisablespellcheck)

## 电子邮件-禁用-自动资本化

一些浏览器试图通过自动大写单词来帮助用户，但是当用户在表单上填写电子邮件地址时，这可能会令人沮丧。将**自动资本化**属性设置为`none`或`off`将禁用该功能。

此规则旨在当用于收集用户电子邮件地址的元素的 autocapitalize 属性未设置为 none 或 off 时发出警告。

### 错误的例子

```
<input type="email" />
<input type="email" autocapitalize="on" />
<input name="email" />
<input id="email" /> 
```

### 正确例子

```
<input type="email" autocapitalize="none" />
<input type="email" autocapitalize="off" />
<input name="email" autocapitalize="off" />
<input id="email" autocapitalize="off" /> 
```

## 邮件-禁用-拼写检查

一些浏览器试图帮助用户拼写检查单词，并用下划线标出拼写错误的单词，但这对于用户在表单上填写电子邮件地址来说可能会令人沮丧。将**拼写检查**属性设置为`false`将禁用该功能。

此规则旨在当用于收集用户电子邮件地址的元素的拼写检查属性未设置为 false 时发出警告。

### 错误的例子

```
<input type="email" />
<input type="email" spellcheck="true" />
<input name="email" />
<input id="email" /> 
```

### 正确例子

```
<input type="email" spellcheck="false" />
<input name="email" spellcheck="false" />
<input id="email" spellcheck="false" /> 
```

## 用户名-禁用-自动资本化

一些浏览器试图通过自动大写单词来帮助用户，但是当用户在表单上填写用户名时，这可能会令人沮丧。将**自动资本化**属性设置为`none`或`off`将禁用该功能。

此规则旨在当用于收集用户名的元素的 autocapitalize 属性未设置为 none 或 off 时发出警告。

### 错误的例子

```
<input name="username" />
<input name="username" autocapitalize="on" />
<input id="username" />
<input id="username" autocapitalize="on" /> 
```

### 正确例子

```
<input name="username" autocapitalize="off" />
<input name="username" autocapitialize="none" />
<input id="username" autocapitalize="off" />
<input id="username" autocapitalize="none" /> 
```

## 用户名-禁用-自动更正

iOS 会尝试帮助用户并自动纠正它认为错误的单词，但这可能会让用户在表单上填写用户名时感到沮丧。将**自动更正**属性设置为`off`将禁用该功能。

此规则旨在当用于收集用户名的元素的“自动更正”属性未设置为“关闭”时发出警告。

### 错误的例子

```
<input name="username" />
<input name="username" autocorrect="on" />
<input id="username" />
<input id="username" autocorrect="on" /> 
```

### 正确例子

```
<input name="username" autocorrect="off" />
<input id="username" autocorrect="off" /> 
```

## 用户名-禁用-拼写检查

一些浏览器试图帮助用户拼写检查单词，并用下划线标出拼写错误的单词，但这可能会让用户在表单上填写用户名时感到沮丧。将**拼写检查**属性设置为`false`将禁用该功能。

此规则旨在当用于收集用户名的元素的拼写检查属性未设置为 false 时发出警告。

### 错误的例子

```
<input name="username" />
<input name="username" spellcheck="true" />
<input id="username" />
<input id="username" spellcheck="true" /> 
```

### 正确例子

```
<input name="username" spellcheck="false" />
<input id="username" spellcheck="false" /> 
```

# 如何使用🛠️

下面是如何在一个已经启用了 ESLint 的 React 项目上使用该插件的指南。

## 安装斯洛文尼亚语插件

```
npm install eslint-plugin-jsx-form --save-dev 
```

或者

```
yarn add eslint-plugin-jsx-form -D 
```

## 向配置中添加插件

编辑您的 ESLint 配置，通常是`.eslintrc`文件，并添加以下内容:

```
{  ...  "plugins":  [...,  "jsx-form"],  "rules":  {  ...,  "jsx-form/email-disable-autocapitalize":  2,  "jsx-form/email-disable-spellcheck":  2,  "jsx-form/username-disable-autocapitalize":  2,  "jsx-form/username-disable-autocorrect":  2,  "jsx-form/username-disable-spellcheck":  2  }  } 
```

或者，除了单独配置规则之外，还可以使用推荐的配置来添加以下内容:

```
{  ...  "extends":  [...,  "plugin:jsx-form/recommended"],  "plugins":  [...,  "jsx-form"],  } 
```

**注:**...引用配置文件中的现有内容。

## 在行动🎬

下面是插件在 VS 代码上运行的截图。如你所见，当你悬停在红色曲线上时，会显示警告。在这种情况下，“将电子邮件的“自动资本化”属性设置为“无”或“关闭”。

[![ESLint Plugin jsx-form In Action on VS Code](img/2bb39bd56cf5429553c9d547f8dab552.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nYim3g1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kgjpm4vje6wr81409gpw.png)

这些违规也可以在 VS 代码的问题窗口中查看。

[![ESLint Plugin jsx-form Problems Window View](img/80bf1655f4504b07341dfc33b60044ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RYg2QDvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wtopvzcpyyz94prtoh2j.png)