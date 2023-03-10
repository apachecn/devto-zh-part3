# 移动网络中的三个问题

> 原文：<https://dev.to/gaserd/three-problems-in-mobile-web-357a>

[![three problems in mobile web](img/68be6838de6931839f57fe2d3b747705.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bfolpm0e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ilm3tpg98727l7ul3ji.png)

## 过度滚动

移动设备中的 Overscroll 只在 windows 上。如果你有一个有自己卷轴的方块，不幸的是，它不能工作，但是对于这个，也有一个**解决方案** :

```
if (this.el.scrollTop <= 0) {
   this.el.scrollTop = 1;
} else if (this.el.scrollTop >= this.el.scrollHeight - this.el.offsetHeight) {
   this.el.scrollTop = this.el.scrollHeight - this.el.offsetHeight - 1;
} 
```

## 固定+变换

最有可能的是，大家都熟悉的问题:如果有一个固定块，他的一个父母应用了变换，那么所有的固定块开始离开。这是一个非常卑鄙的错误，有时会使我们的移动界面的动画非常糟糕。

**解法**:记住变换前固定块的位置，用 top 属性把它们变成绝对。

这有点复杂，但总的来说，如果你需要，你可以处理它。

## 平安无事

由于我们是在移动网络上，我们有大量的设备，其中之一是带有新框架的 iPhone X。

如果这些框架出来后，你的界面看起来没有你想要的那么漂亮，该怎么办？

**解决方案**:使用此文档【https://ayogo.com/blog/ios11-viewport/ T2】

你知道移动 web 中的哪些问题？