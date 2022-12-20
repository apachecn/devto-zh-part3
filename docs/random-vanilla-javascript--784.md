# 随机香草 Javascript

> 原文：<https://dev.to/independnt/random-vanilla-javascript--784>

到目前为止，我一直试图让自己对 ruby、ruby on rails、React、CSS、HTML、Javascript 算法和一些 JQuery 保持新鲜感。然而，最近我参加了一个工作面试，面试问了我除了 JQuery 之外的所有技能。相反，他们实际上讨厌 JQuery，并且已经把它从他们的应用程序中完全删除了。他们说，我引用他们的话“感谢上帝，我们已经在两周前从应用程序中删除了所有的 JQuery，现在我们运行的是纯普通的 Javascript”。现在我明白了为什么这对他们的应用来说是件好事，但对我来说呢？这没什么大不了的，因为我的普通 Javascript 水平可能被一些人称为“呃”。所以我坚持每天做一点普通的 Javascript，一次做一个小项目，我学到了一些以前不一定知道的小而杂的东西。

现在我知道了所有关于 DOM 的知识，我很清楚如何操作它，甚至如何选择我想要更改的项目。在开始我的普通 Javascript 挑战之前，我主要依赖于“document.selectbyID”或“document.selectbyClassName”之类的东西，但完全不知道“document.querySelector”和“document.querySelectorAll”。两者都可以有效地用于抓取某些元素。关于这两个要注意的重要小花絮是，查询选择器返回一个节点，而 querySelectorAll 返回一个节点列表，所以它不会附带您可能习惯的所有常见数组命令。查看它的原型，您会发现您可以利用像 forEach()这样的函数，但其他函数不多。不用怕，你随时可以使用“Array.from()”。另一个很大的区别是语法和浏览器支持。SelectById 和 SelectByClassName 已经存在很长时间了，所以继续使用它们可能更安全，但不管怎样都很有趣！

查询选择器也可以用作 CSS3 选择器。将一个变量设置为一个类的 queryselector 可以被修改，这很方便。例如:

```
 <div class="Random-line"></div> 
const example = document.querySelector('.Random-line')

example.style.transform = `rotate(90 deg)` 
```

现在，这将进入 CSS，我们可以设置我们的 transoform 风格为 90 度，使线移动 90 度。

此外，在使用一个有几个不同控件的应用程序时，其中一个是“范围”类型的输入，另一个是“颜色”,我可以通过执行以下操作获得所有控件:

```
 const inputs = document.querySelectorAll(`.controls input`) 
```

现在我有了页面上所有控件的所有输入！此外，我们能够使用 forEach 在所有这些服务器上添加 eventListeners！

```
 inputs.forEach(input => input.addEventListener('change', handleUpdate)) 
```

现在我们可以创建一个 handleUpdate 函数来监听变化并处理这些更新！非常酷。这些已经派上了用场，我相信将来还会继续派上用场。