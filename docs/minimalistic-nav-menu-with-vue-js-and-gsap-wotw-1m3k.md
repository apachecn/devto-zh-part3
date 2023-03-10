# 带有 Vue.js 和 GSAP - WotW 的极简导航菜单

> 原文：<https://dev.to/ederchrono/minimalistic-nav-menu-with-vue-js-and-gsap-wotw-1m3k>

欢迎来到“每周小部件”系列的第二季，在这里我拍摄了令人敬畏的 UI/UX 组件的 gif 或视频，并用代码将它们赋予生命。

> 距离我上次发帖已经很久了。我一直忙于一些项目(包括生一个女孩)，但我带着更多的知识和小工具回来了。

今天我们将创建一个极简的导航菜单，当鼠标悬停在一个选项上时，它会显示动画。
灵感来自[珍雅·伦祖克](https://dribbble.com/Zhenya_Artem)创作的[作品](https://dribbble.com/shots/6109210-Chez-Studio-Menu-Hover-Animation)，看起来是这样的:

[![wotw-pass](img/951b53ecc9a4fbd67e48601d8550e3ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lAxq6nH1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qf0kn2nj327wtx92f2g0.gif)

### 这是给谁的？

本教程面向希望提升技能的前端开发人员。建议你先有一些 HTML，CSS，JS 的知识。
我将使用 [Vue.js](https://vuejs.org/) 来制作这个小工具，如果你不熟悉这个框架，这些精彩的帖子可以帮助你快速上手:

*   [用 Vue.js 替换 jQuery](https://www.smashingmagazine.com/2018/02/jquery-vue-javascript/)
*   [从 React 切换到 Vue.js](https://medium.com/js-dojo/switching-from-react-to-vue-js-badf34565a2d)

### 制剂

对于今天的小部件，我们将使用 [Vue.js](https://vuejs.org/) ，对于一些动画，我们将使用 [TweenMax](https://greensock.com/tweenmax) 。如果你想继续，你可以派生出这个已经有依赖关系的 [codepen 模板](https://codepen.io/ederdiaz/pen/gzyxWv)。

### 匹配外观

这个组件我想做的第一件事就是搭配设计。这可能是最简单的部分，因为没有复杂的图形需要`SVG`或图标。

在我们的应用程序节点中，我将首先为菜单创建标记。因为这将是一个导航菜单，我们应该使用语义正确的 HTML 节点:

```
<!-- HTML -->
<div id="app">
  <nav>
    <ul>
      <li class="menu-item">About</li>
      <li class="menu-item">Works</li>
      <li class="menu-item">News/Blog</li>
      <li class="menu-item">Contact us</li>
    </ul>
  </nav>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在下一步中，我将添加`class="menu-item"`来设计容器和菜单项的样式。此外，`id="app"`将在稍后使用`Vue.js`添加功能和动画。

菜单现在看起来真的很糟糕，让我们改变一下

```
/* CSS */
body {
  background-color: #f1f0e9;
}

.menu-item {
  font-size: 5em;
  list-style: none;
  text-transform: uppercase;
  font-family: sans-serif;
  text-align: center;
  cursor: pointer;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该有这样的东西:
[![styled](img/9fa433e2abaee26f9f403070198eefdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0VWYufya--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b3jusw2apvna9eio0s0r.png)

让我解释一下最重要的规则...

*   正在帮助从列表中删除项目符号。
*   `text-transform: uppercase;`在我们不想用屏幕阅读器显式地“吼”用户，只想出于设计目的显示大写字符的时候很有用。
*   使鼠标表现得好像每个元素都是一个链接。

### 设置视图. js

在进入更多细节之前，让我们将 Vue.js 添加到组件中，以便能够动态呈现菜单项。

```
// JS
const itemsList = ['About', 'Works', 'News/Blog', 'Contact us']

new Vue({
  el: '#app',
  computed: {
    menuItems() {
      return itemsList.map((item, index) => {
        return {
          label: item
        }
      })
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

> 我将元素作为常量的计算属性添加，而不是将数据添加到组件中，因为我需要将它们绑定到我的模板，但是它们不会随时间而改变。

现在我们已经有了一个列表，并且它们已经准备好被用作一个计算属性，我们可以简化我们的模板来呈现所有带有`v-for` :
的菜单项

```
<!-- HTML - inside div > nav > ul -->
<li v-for="(item, index) in menuItems" :key="`item-${index}`" class="menu-item">
  {{item.label}}
</li>
<!-- remove all other "li" elements --> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该有相同的元素，只是绑定到我们的 Vue.js 实例。

### 悬停和动画

参考文献中的菜单动画可以分为两部分，第一部分是向左移动菜单项，第二部分是翻转字符。

让我们从第一个开始，将鼠标光标下方的菜单向左移动。为此，我们将在菜单项中添加一个`@mouseover`事件，该事件将触发一个名为`selectedItem()`的函数，我们还没有声明这个函数:

```
<!-- HTML inside nav > ul -->
<li
  v-for="(item, index) in menuItems"
  :key="`item-${index}`"
  @mouseover="selectItem(index)"
  class="menu-item"
>
  {{item.label}}
</li> 
```

Enter fullscreen mode Exit fullscreen mode

现在对于`selectItem()`方法，我们想要跟踪悬停的项目，所以我们将在组件数据中添加一个`selectedItem`变量。如果没有选择任何项目，这个属性将从`-1`开始，当鼠标悬停时，它的值将变为所选按钮的索引。

```
// JS
new Vue({
  el: '#app',
  data: {
    selectedItem: -1
  },
  methods: {
    selectItem(id) {
      this.selectedItem = id;
    }
  },
  // ... the rest of our component 
```

Enter fullscreen mode Exit fullscreen mode

要查看`selectedItem`的变化，您可以在模板中添加下一行:

```
<!-- HTML after </nav> -->
<p>
  selectedItem: {{ selectedItem }}
</p> 
```

Enter fullscreen mode Exit fullscreen mode

知道哪个按钮当前被选中将让我们添加一个类来“移动”我们的按钮。为此，我们可以将一个`selected`属性添加到我们计算的`menuItems`中，如下所示:

```
// JS inside computed
menuItems () {
  return itemsList.map((item, index) => {
    const isSelected = this.selectedItem === index;
    return {
      label: item,
      selected: isSelected
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

并在 HTML 中使用这个新属性:

```
<!-- HTML inside nav > ul -->
<li
  v-for="(item, index) in menuItems"
  :key="`item-${index}`"
  @mouseover="selectItem(index)"
  :class="{'selected': item.selected}"
  class="menu-item"
>
  {{item.label}}
</li> 
```

Enter fullscreen mode Exit fullscreen mode

> 注意有两个类属性，它们连接在一起，而不是互相覆盖。当“item.selected”为“true”时，该菜单项将同时具有“menu-item”和“selected”类。

让我们添加 CSS 类来处理移动:

```
/* CSS */
.menu-item {
  /* ... previous styles */
  transition: margin-left 0.5s ease-out, opacity 0.5s ease-out;
}

.selected {
  margin-left: -90px;
} 
```

Enter fullscreen mode Exit fullscreen mode

> transition 属性表示对该值的任何更改都应该是动画。

我们几乎完成了这一部分，但是还缺少一些东西。将鼠标移出所有元素后，最后一个元素保持选中状态，这是我们不希望的。为了解决这个问题，我们可以使用`@mouseleave`事件:

```
<!-- HTML inside nav > ul -->
<li
  v-for="(item, index) in menuItems"
  :key="`item-${index}`"
  @mouseover="selectItem(index)"
  @mouseleave="selectItem(-1)"
  :class="{'selected': item.selected}"
  class="menu-item"
>
  {{item.label}}
</li> 
```

Enter fullscreen mode Exit fullscreen mode

### 添加箭头并调暗其他项目

在进入角色的动画之前，仍然有一些细节，应该有一个箭头出现在选中的项目上，并且未选中的项目应该变暗或半透明。

让我们快速添加一个箭头字符到我们的菜单项。

```
<!-- HTML inside nav > ul -->
<li
  v-for="(item, index) in menuItems"
  :key="`item-${index}`"
  @mouseover="selectItem(index)"
  @mouseleave="selectItem(-1)"
  :class="{'selected': item.selected}"
  class="menu-item"
>
  {{item.label}}
  <span class="arrow">➔</span>
</li> 
```

Enter fullscreen mode Exit fullscreen mode

> 箭头看起来与参考略有不同，因为它是一个常规的➔字符，而不是一个与设计完全匹配的向量，但对于我们的目的来说已经足够好了。

我们想要隐藏所有的箭头，除非它们是一个被选择的条目的子条目，我们可以用 CSS 规则来做到这一点，就像我们之前对被选择的条目所做的一样:

```
/* CSS */
.menu-item > .arrow {
  opacity: 0;
  transition: opacity 0.7s ease-out;
}

.selected > .arrow {
  opacity: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在箭头出现又消失，让我们将未选择的项目变暗。我们可以计算变暗的项目，就像我们计算选中的项目一样:

```
// JS inside computed
menuItems () {
  return itemsList.map((item, index) => {
    const isSelected = this.selectedItem === index;
    const otherButtonIsSelected = this.selectedItem !== -1
    return {
      label: item,
      selected: isSelected,
      dimmed: !isSelected && otherButtonIsSelected
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

...将`.dimmed`类添加到我们的 HTML:
中的项目中

```
<!-- HTML inside nav > ul -->
<li
  v-for="(item, index) in menuItems"
  :key="`item-${index}`"
  @mouseover="selectItem(index)"
  @mouseleave="selectItem(-1)"
  :class="{
      'selected': item.selected,
      'dimmed': item.dimmed
    }"
  class="menu-item"
>
  {{item.label}}
  <span class="arrow">➔</span>
</li> 
```

Enter fullscreen mode Exit fullscreen mode

...最后但同样重要的是，创建`.dimmed` CSS 规则:

```
/* CSS */
.dimmed {
  opacity: 0.3;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们越来越接近最终产品。

[![basic-animations](img/4c8784914679b2141ab677821dcc73a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D5gbynbR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mmmqeeynf50y8rocq6ev.png)

### 角色翻转动画

翻转每个菜单项字符可能是这个小部件中最有趣和最复杂的部分。我们不能只翻转整个菜单，每个字符都应该单独水平翻转(`scaleX: -1`)。

为了能够“控制”每一个字符，我们需要拆分菜单字符:

```
// JS inside computed
menuItems () {
  return itemsList.map((item, index) => {
    const isSelected = this.selectedItem === index;
    const otherButtonIsSelected = this.selectedItem !== -1
    return {
      label: item,
      selected: isSelected,
      dimmed: !isSelected && otherButtonIsSelected,
      chars: item.split('')
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

...使用`chars`属性，我们现在可以呈现一个`<span>`节点中的每个字符:

```
<!-- HTML inside nav > ul -->
<li
  v-for="(item, index) in menuItems"
  :key="`item-${index}`"
  @mouseover="selectItem(index)"
  @mouseleave="selectItem(-1)"
  :class="{
      'selected': item.selected,
      'dimmed': item.dimmed
    }"
  class="menu-item"
>
  <span
    class="char"
    v-for="(char, charIndex) in item.chars"
    :key="`char-${charIndex}`"
    :ref="`char-${index}-${charIndex}`"
    >{{char}}</span
  >
  <span class="arrow">➔</span>
</li> 
```

Enter fullscreen mode Exit fullscreen mode

> 注意“ref”属性，它将帮助我们使用菜单项的“index”和“charIndex”来“引用”所有这些字符。

此时，视觉上应该没有什么变化，但是我们应该把菜单分成不同的字符。

我们将添加几个常量，帮助我们更好地阅读代码的下一部分

```
// JS below const itemList declaration
const LEFT = -1
const RIGHT = 1 
```

Enter fullscreen mode Exit fullscreen mode

在那之后，每当`selectedItem`改变，我们想要开始正确的动画所有的角色。我们将循环每个`menuItem`，根据它们是否被选中，我们将翻转它们`LEFT`或`RIGHT` :

```
// JS inside methods
selectItem(id) {
  this.selectedItem = id;

  this.menuItems.forEach((item, index) => {
    const direction = item.selected ? LEFT : RIGHT;
    this.animateChars(index, item.label.length, direction);
  })
}, 
```

Enter fullscreen mode Exit fullscreen mode

`animateChars()`方法还没有声明，但是现在应该不难创建，因为我们已经有了项目的索引、项目的字符数和翻转字母的方向:

```
// JS inside methods
animateChars (id, charLength, direction) {
  for(let c=0;c < charLength; c++){
    const refId = `char-${id}-${c}`;
    const char = this.$refs[refId];
    TweenMax.killTweensOf(char);
    TweenMax.to(char, 0.5, {scaleX: direction});
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 在这个方法中，我们基本上是为每个角色获取参考，杀死当前拥有的动画(如果有的话)，然后将角色翻转到我们之前计算的方向。

看起来一切都应该看起来像参考，但它没有😰。

如果我们查看控制台，我们会看到字符被正确转换，正如我所料，TweenMax 正在更改它们的转换矩阵:

[![oh-no](img/2177880a9d3572c7577acf8951733b2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--czz2ft3t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zfssr71knqt18483gw22.png)

在抓了一会儿脑袋并寻找任何错误之后，我发现我们正在制作动画的`<span>`节点，默认情况下将它们的`display`属性设置为`inline`。转换似乎不能处理这种类型的显示属性，所以让我们利用添加到这些元素中的`.char`类来解决这个问题:

```
.char {
  display: inline-block;
  min-width: 0.3em;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 添加“最小宽度”属性是为了使文本看起来正确，当添加“内嵌块”显示属性时，像空格这样的字符会失去它们的宽度。

而现在最后的结果！

[https://codepen.io/ederdiaz/embed/KEbOZd?height=600&default-tab=result&embed-version=2](https://codepen.io/ederdiaz/embed/KEbOZd?height=600&default-tab=result&embed-version=2)

我知道还有改进的空间，有时动画运行不流畅是因为所有不同的元素渲染。如果你有任何改进的想法，我很乐意在评论中看到。

这就是本周的**小部件。**

如果你想知道更多，你可以去看看其他的 WotW:

*   [密码验证器](https://dev.to/ederchrono/making-an-interactive-password-validator---wotw-1md0)
*   [动画滑块控件](https://dev.to/ederchrono/making-an-animated-slider---wotw-mkj)
*   [3D facing widget](https://dev.to/ederchrono/making-a-3d-facing-widget---wotw-1700)

最初发布于 [ederdiaz.dev](https://ederdiaz.dev)