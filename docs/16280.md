# 可搜索选择🤔

> 原文：<https://dev.to/salvan13/searchable-select--1f7a>

## 选择

HTML *select* 元素非常有用，但有时我们需要更强大的东西，当我们有数百个选项(例如世界上所有国家的列表)时，让用户能够过滤元素会很好，不幸的是，我们没有为它提供*可搜索*或*可过滤*属性

```
<label for="country">Please select your country</label>
<select id="country" name="country" required>
  <option value="it">Italy<option>
  <option value="fr">France<option>
  ... and so many other options ...
<select> 
```

## 输入

使用本地 HTML 元素在列表中搜索元素的一种方法是使用*输入*加上*数据列表*

```
<label for="country">Please select your country</label>
<input id="country" name="country" required list="country-list" />
<datalist id="country-list">
  <option value="Italy">
  <option value="France">
  ...
</datalist> 
```

这样我们就有了一个带提示的输入，但是用户仍然可以写任何他想写的东西。所以我们需要验证它！

## 验证

让我们使用*模式*属性来验证输入

```
<label for="country">Please select your country</label>
<input id="country" name="country" required list="country-list"
  pattern="Italy|France" title="Please select a country" />
<datalist id="country-list">
  <option value="Italy">
  <option value="France">
  ...
</datalist> 
```

酷，现在我们有了一个带有可搜索选项的输入，并且只允许定义的选项！

# 问题

我在这里看到的唯一问题是，乍一看不是很清楚这不是正常的文字输入，而是一个(我来说)*可搜索选择*。

只有在用户交互后，下拉菜单才会显示。

# 结论

HTML 给了我们许多工具，让我们看看在构建我们自己解决方案之前，是否可以使用和组合它们😃

让我知道你对这种方法的看法！