# 创建自定义键盘辅助复选框

> 原文：<https://dev.to/lkopacz/create-custom-keyboard-accessible-checkboxes-2036>

[https://api.parler.io/ss/player?url=https://www.parler.io/audio/9625517181/c35f66502ddd3b9b56a4db767e00d2e69455810c.36b95f33-8819-457e-be2e-6d137985f731.mp3](https://api.parler.io/ss/player?url=https://www.parler.io/audio/9625517181/c35f66502ddd3b9b56a4db767e00d2e69455810c.36b95f33-8819-457e-be2e-6d137985f731.mp3)

我见过很多设计师设计出这些华丽的复选框样式，但是当你看到它们被实现时，你甚至不能用键盘来选择它们。假设我们从我们的设计师那里得到了这个风格指南。

[![Checkboxes design with teal color when checked and a black checkmark](img/1b760528e5b4983e78dfab1d87499fc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O_xDZ3BA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/teal-checkboxes-design-9e42a8bdd05057fd3901bc435050a03c-d6cb8.png)

我以前见过这样的实现，看起来很棒。然而，当我按下`tab`键时，它就滑过去了。如果这个字段是必需的，你就在欺骗你的一群用户。他们使用`::before`或`::after`伪元素来制作一个漂亮的复选框，并使用`:checked`伪类来确定检查本身的样式。它看起来很酷，但问题是他们在复选框输入本身上使用了`display: none`。当我们这样做时，我们使浏览器看不到复选框本身，使那些依靠键盘导航网站的人无法使用它。

[![Gif of keyboard trying to access the custom checkmarks but skipping to the link.](img/a18e5f335f562f79e4df720c18e041e7.png)](https://i.giphy.com/media/3Fd7rUatwPzyAloC5O/giphy.gif)

## 起点

让我们一步一步来看我将如何经历这一切。下面是我的起始代码的样子:

```
<fieldset>
  <legend>Accessible Checkboxes</legend>

  <input type="checkbox" name="Checkbox" id="check_1">
  <label for="check_1">Checkbox</label>

  <input type="checkbox" name="CSS Only" id="css_only">
  <label for="css_only">CSS Only</label>

  <input type="checkbox" name="" id="disabled_sample" disabled>
  <label for="disabled_sample">A disabled checkbox</label>

  <input type="checkbox" name="Fourth Option" id="fourth_check">
  <label for="fourth_check">Fourth Option</label>
</fieldset> 
```

[![Basic checkboxes with no design.](img/b063a5caf2134eb05c8076e6d4aee84e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ViPzbYlN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/Starting-point-e7d8617e0b896100dfc7cdd0651df4bb-e70c4.png)

我会从一个基本的复选框列表开始。以下是我目前拥有的 CSS:

```
input[type="checkbox"] {
  position: absolute;
}

input[type="checkbox"] + label {
    display: block;
    position: relative;
    padding: 0 1.5rem;
} 
```

## 在标签上创建一个伪元素

我想做的第一件事是确保我创建了一个可以代替我的复选框的伪元素。我要做的是在`<label>`元素上创建一个`::before`伪元素。现在看起来是这样的:

```
input[type="checkbox"] + label::before {
  content: '';
  position: relative;
  display: inline-block;
  margin-right: 10px;
  width: 20px;
  height: 20px;
  background: white;
} 
```

[![Checkboxes with both a white box and a normal checkbox.](img/d8918a1fd5921271c097926dab282ecb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--inaCyZkI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/checkboxes-with-psuedo-5e8102fb292c6fc15a922c4e77f2d983-be790.png)

我故意在那里留下了无风格的原创复选框。这样做的原因是它让我更容易判断一个复选框何时被聚焦、选中等。这有助于我直到最后一刻才隐藏复选框。

## 选中时在伪元素上添加样式

到目前为止，当我们尝试选中复选框时，除了正常行为之外，它不会做任何事情。我们要做的是使用`:checked`伪类添加一点 CSS 魔法。见下图:

```
input[type="checkbox"]:checked + label::before {
  background: #5ac5c9;
} 
```

[![Checkboxes with both a white box and a normal checkbox. One is checked and the box next to it is teal.](img/6cc1994451bbca3e84a126423f3b2307.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Eo2JEilY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/teal-checkbox-0879c2ab147316c10116e9689c393503-84a27.png)

## 添加您的自定义勾号

如果您想对`::before`元素的内容做一个复选标记 unicode，您完全可以这样做。不过，我想弄得花哨一点。现在，我们要确保在我们的自定义元素中有一个垂直的复选标记。我已经通过添加一个`::after`伪元素做到了这一点。我们在这里做的是创建一个有两条边的直角并旋转它。

```
input[type="checkbox"]:checked + label::after {
  content: '';
  position: absolute;
  top: 3px;
  left: 27px;
  border-left: 2px solid black;
  border-bottom: 2px solid black;
  height: 6px;
  width: 13px;
  transform: rotate(-45deg);
} 
```

[![Teal checked box with a normal checked checkbox next to it.](img/4abe087f3405bd42ef7aac69db8231b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8ehrM6E5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/teal-checkbox-with-check-a69ccbedad8dae7327fe8f7179e76aaa-71b8d.png)

一个额外的挑战，而不是检查，使一个“x”

## 给伪元素添加焦点样式

太好了！我们现在可以走了吗？不完全是。

我们仍然需要确保伪元素“获得焦点”我们现在要做的是在复选框获得焦点时复制焦点样式。我们不想做`display: none`的原因是因为移除显示会阻止复选框获得焦点。我想有一些具体的焦点样式，因为它们可以因浏览器而异。下面是我最后做的，因为我想复制 Chrome 的默认焦点，但是是在所有的浏览器中。不一样，但是很接近！

```
input[type="checkbox"]:focus + label::before {
  outline: #5d9dd5 solid 1px;
  box-shadow: 0 0px 8px #5e9ed6;
} 
```

[![Boxed item has a blue item around it, indicating the keyboard is focused on it.](img/de57b08fca4c3616579c5fa2b3ee5688.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BiHrqXtd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/receiving-focus-adc031ec052971fa7b4f7e1941c9bb47-4e598.png)

现在我们可以隐藏它原来的复选框！当我们解决这个问题的时候，看到它有多有用了吗？

```
input[type="checkbox"] {
  position: absolute;
  height: 1px; 
  width: 1px;
  overflow: hidden;
  clip: rect(1px 1px 1px 1px); /* IE6, IE7 */
  clip: rect(1px, 1px, 1px, 1px);
} 
```

(以上代码是常见的[可视化隐藏 css](https://a11yproject.com/posts/how-to-hide-content/) )

[![Gif of keyboard focusing on custom checkboxes and checking](img/dbd2480b634977c81603a4e69df991c2.png)](https://i.giphy.com/media/1ynEvIv4dpGRPB7cyI/giphy.gif)

## 为禁用的复选框添加一些样式

最后一件事，我们可能应该使禁用复选框风格不同。下面是我做的:

```
input[type="checkbox"]:disabled + label {
  color: #575757;
}

input[type="checkbox"]:disabled + label::before {
  background: #ddd;
} 
```

[![Disabled checkbox with dark grey box.](img/2a84b7cb46a2a0a498c1e055359bb5a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ZT9V6Qj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/disabled-checkbox-6305c9b3e88c371c0615e1498f44a354-793e6.png)

原来如此！您也可以将相同的原则应用于单选按钮。请在 [Twitter](https://twitter.com/littlekope0903) 上告诉我，或者在下面评论你的想法！

**声明:**我差点没贴这个。昨晚当我浏览 Twitter 时，我看到了一个帖子，里面有几乎相同的提示。在问了 Twitterverse 我是否应该发布它之后，我得到了压倒性数量的人说“是的！”另一本我没看过，只是略读，多半是不想影响写作。然而，我想为你链接一下，以防你想阅读:[如何定制可访问的复选框和单选按钮](https://webdesign.tutsplus.com/tutorials/how-to-make-custom-accessible-checkboxes-and-radio-buttons--cms-32074)。