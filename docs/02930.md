# 更易访问的表格

> 原文：<https://dev.to/wozzo/more-accessible-tables-59an>

所以 tldr，使用带有`scope`属性的`<th>`标签！如果你想知道什么是`scope`属性，欢迎来到大约 3 天前的我的生活，请继续阅读。

长期以来，表格一直是 HTML 的核心部分。早在 1996 年，RFC 1942 就引入了它们，从那以后我就一直做错。最初，我的标记只包含了得到一个显示
的表格的最低要求

```
<table>
  <tr>
    <td>Cereal</td>
    <td>Weight</td>
    <td>Cost</td>
  </tr>
  <tr>
    <td>Corn flakes</td>
    <td>450g</td>
    <td>77p</td>
  </tr>
  <tr>
    <td>Shreddies</td>
    <td>750g</td>
    <td>£1.10</td>
  </tr>
</table> 
```

大多数开发人员会立即发现这里缺少了很多东西，但是这很有效，为什么我们还需要更多呢？因为这让包括开发者在内的所有人的生活都变得更加轻松。考虑想要上面的样式。如果你想让标题行有不同的格式，你的选择器会比它需要的更复杂。排队我的表进化的下一个阶段。

```
<table>
  <thead>
    <tr>
      <th>Cereal</th>
      <th>Weight</th>
      <th>Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Corn flakes</td>
      <td>450g</td>
      <td>77p</td>
    </tr>
    <tr>
      <td>Shreddies</td>
      <td>750g</td>
      <td>£1.10</td>
    </tr>
  </tbody>
</table> 
```

所以标题行被包装在`<thead>`标签中，其余的被包装在`<tbody>`标签中(如果你需要的话，还有`<tfoot>`标签)，标题中的`<td>`也被改成了`<th>`。这做了几件事，让我们所有人的生活更轻松。
作为一名开发人员，使用`<thead>`、`<tbody>`、&、`<tfoot>`标签来分隔表格的各个部分，我发现这让我更容易找到我需要处理的部分。对于我们的用户来说，他们可以让浏览器在滚动正文的同时冻结标题。
`<th>`标签现在可以用来轻松地设置标题的样式，并在使表格更易访问方面发挥重要作用。当用屏幕阅读器阅读表格时，用户可以让阅读器阅读表格中任何位置的单元格标题，但是仍然有一个问题。如果用户选择了 1.10 的单元格，它只会显示“价格 1.10”，而不会告诉用户它的价格是多少。
解决这个问题需要两步。首先，向现有的`<th>`添加一个值为“col”的`scope`属性。这明确地告诉屏幕阅读器，标题是针对该列中的单元格的，其次，我们应该将行标题更改为也具有`scope`属性的`<th>`单元格。

```
<table>
  <thead>
    <tr>
      <th scope="col">Cereal</th>
      <th scope="col">Weight</th>
      <th scope="col">Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Corn flakes</th>
      <td>450g</td>
      <td>77p</td>
    </tr>
    <tr>
      <th scope="row">Shreddies</th>
      <td>750g</td>
      <td>£1.10</td>
    </tr>
  </tbody>
</table> 
```

现在屏幕读者知道我们的 1.10 是 shreddies 的价格。对于我们开发人员来说，额外的好处也是一样的，因为我们现在可以将行标题的样式与行中的其他`<td>`分开。

最后一个技巧是给表格添加一个`<caption>`标签，这样就完成了。

HTML 被设计成能被尽可能多的人访问，但是这种语言并没有强制执行。这意味着像我这样的开发人员可以持续 20 多年而没有意识到这些特性的存在。请在评论中分享你经历过的类似时刻。