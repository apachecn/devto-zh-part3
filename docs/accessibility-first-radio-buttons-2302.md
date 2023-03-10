# 易访问性优先:单选按钮

> 原文：<https://dev.to/link2twenty/accessibility-first-radio-buttons-2302>

这将是我的可访问性第一系列中的最后一个元素。我们已经制作了材料设计开关、材料设计输入框，现在我们即将制作材料设计单选按钮。

这个简短系列的目的是展示你可以把易访问性放在第一位，并且仍然使用好的设计。其中大多数都是其基本元素的非常简单、风格化的版本。

## 我们要创造什么？

今天我们将制作一个受材料设计启发的[单选按钮](https://material.io/design/components/selection-controls.html#radio-buttons)。和往常一样，我会避免使用 JavaScript，不是因为它本质上不好，而是我想尽可能地依赖这个平台，这样会有更好的用户体验。

[https://jsfiddle.net/link2twenty/b81ev7wp//embedded/result//dark](https://jsfiddle.net/link2twenty/b81ev7wp//embedded/result//dark)

如果你不知道单选按钮是按名称分组的，你可以切换到每个命名的组。跳转到该组只会选择选中的按钮，但您可以使用箭头键来移动焦点，然后选中新的焦点按钮。

## 标记

我毫不怀疑，如果你看过我的任何东西，你现在习惯了这种风格，标签包装输入，我稍后会隐藏。

```
<label class="md_radio">
  <input name="{{group name}}" type="radio" />
  <span class="md_radio__button"></span>
  Radio Button
</label> 
```

Enter fullscreen mode Exit fullscreen mode

[https://jsfiddle.net/link2twenty/apj94sue//embedded/html,result//dark](https://jsfiddle.net/link2twenty/apj94sue//embedded/html,result//dark)

## 样式

单选按钮和开关之间有如此多的相似之处，所以我不会在这篇文章中详细介绍，但我仍然会给你一个大概的印象。

我们必须考虑的状态有

*   未检查
*   检查
*   有缺陷的
*   聚焦的

### 未勾选

我们也将在这里进行所有的设置，因为未选中是通常的默认状态。

#### 设置

在这里，我们只是设置一些默认的空白，显示类型和设置材料字体。

```
@import url(http://fonts.googleapis.com/css?family=Open+Sans);
.md_radio {
  display: inline-flex;
  font-family: "Open Sans";
  align-items: center;
  margin: 5px 0;
}

.md_radio .md_radio__button {
  position: relative;
  cursor: pointer;
  margin: 0 3px;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 按钮

我使用了`::before`和`::after`伪类来制作按钮，你会注意到我的`::after`样式已经设置好了，但是用 scale 隐藏起来了，这样我们就不用在每次按钮被选中的时候用[来绘制](https://csstriggers.com/transform)。

```
.md_radio .md_radio__button::before,
.md_radio .md_radio__button::after {
  content: '';
  box-sizing: border-box;
  display: block;
  transition: all 100ms cubic-bezier(0.4, 0.0, 0.2, 1);
}

.md_radio .md_radio__button::before {
  height: 1.2em;
  width: 1.2em;
  border-radius: 50%;
  border: 0.15em solid #BDBDBD;
}

.md_radio .md_radio__button::after {
  position: absolute;
  top: 50%;
  left: 50%;
  height: 0.65em;
  width: 0.65em;
  border-radius: 50%;
  transform-origin: 50%, 50%;
  transform: scale(0, 0) translate(-50%, -50%);
  background: #00897B;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Material Design Radio Buttons Not checked](img/02b159d4f31ccc634874baad40347c84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yi9drW3b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ha4pw5x2bg0s3s3uaimj.png)

### 勾选

这个例子非常简单，我们想要放大`::after`并改变`::before`的边框颜色，就这样。

```
.md_radio [type=radio]:checked+.md_radio__button::after {
  transform: scale(1, 1) translate(-50%, -50%);
}

.md_radio [type=radio]:checked+.md_radio__button::before {
  border-color: #00897B;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Material Design Radio Buttons Checked](img/f262452501977aeb32cfaaa1de3f5a60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5BiwVBb0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o26rx8t2vqifukea5u7s.png)

### 禁用和聚焦

这些样式与我的 switch 示例完全相同，所以下面是代码。

```
/* Disabled */
.md_radio [type=radio]:disabled+.md_radio__button {
  cursor: not-allowed;
  filter: grayscale(100%);
  opacity: 0.6;
}

/* Focused */
.md_radio [type=radio]:focus+.md_radio__button {
  outline: #5d9dd5 solid 1px;
  box-shadow: 0 0 8px #5e9ed6;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 整理完毕

最后，我们需要隐藏我们的原始输入我这样做是通过使位置绝对，不占用空间，设置不透明度为 0，并关闭指针事件。

```
.md_radio [type=radio] {
  position: absolute;
  opacity: 0;
  pointer-events: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://jsfiddle.net/link2twenty/b81ev7wp//embedded/result,html,css//dark](https://jsfiddle.net/link2twenty/b81ev7wp//embedded/result,html,css//dark)

谢谢你陪我走完这段旅程。我希望你能从中学到一些东西，并将其应用到你自己的项目中。