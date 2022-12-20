# 构建一个带有嵌套下拉菜单的纯 CSS 菜单

> 原文：<https://dev.to/felipperegazio/building-a-pure-css-menu-with-nested-dropdowns-hcn>

嗯，我们的任务是使用

以下是代码笔的结果:

[https://codepen.io/felipperegazio/embed/BeYBBM?height=600&default-tab=result&embed-version=2](https://codepen.io/felipperegazio/embed/BeYBBM?height=600&default-tab=result&embed-version=2)

当下拉菜单水平增长时，它不会完全响应。而且还用悬停当触发器，这在 mobo 上肯定是个问题。你可以改变它的生长方式，或者把它改成汉堡边栏或者其他什么，这是另一篇文章的主题。反正这段代码还是省了不少 javascript。

现在，我们将关注简单地构建下拉结构。对于这篇文章，你只需要 html 和 css/scss 的基本知识。

## 初始结构

我们最初的 html 结构将被用来创建一个导航栏来保存我们的菜单，必须是这样的:

```
<div class="menu">
  <ul> 
    <li class="link">
      <a href="">This is a link</a>
    </li>
    <li>
      This will open a dropdown soon
    </li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

你可以在任何地方添加下拉菜单。你不应该，但你可以。这里，我们将添加我们的导航条。因此,“菜单”div 将被固定在页面的顶部。您也可以使用一个

<nav>标签。下面是修复顶部 div 的 css。
</nav>

```
.menu {
  // define the height of the menu
  --menu-height: 40px;
  // holder and ul general style
  box-sizing: border-box;
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你不熟悉 css vars，可以看看这个帖子:
[https://dev . to/Sarah _ 马驰/an-introduction-to-CSS-variables-cmj](https://dev.to/sarah_chima/an-introduction-to-css-variables-cmj)

- menu-height 是一个 css 变量，它将定义我们的导航条的高度。这将有助于定义它的子位置。

现在我们有了固定的 nav，重要的是要注意，当瞄准其中的 ul 时，我们有三个范围:

1.  我们可以瞄准所有的 ul(整个范围)。对于整个范围，您只需使用:"。菜单 ul”作为选择器。

2.  我们只能瞄准第一个 ul(内部范围)。这是菜单架，将是菜单的第一级。您可以使用以下方式选择它:“”。菜单> ul”，我们稍后会详细讨论。

3.  我们可以只针对嵌套的 ul(嵌套范围)。这将是我们的下拉菜单。您可以使用以下方式选择它们:菜单> ul li ul”，这将选择所有 ul 的内部列表项，顺便说一下，这些列表项将被放置在固定 div 的第一个 ul(菜单容器)内。

因此，首先我们将针对所有的 ul，以便为它们添加一个共同的外观，避免代码重复。

请注意，我们还设计了 

```
.menu {
  // ...
  ul {
    list-style: none;
    padding: 16px;
    margin: 0;
    li, li a {
      opacity: .8;
      color: #ffffff;      
      cursor: pointer;
      transition: 200ms;
      text-decoration: none;
      white-space: nowrap;
      font-weight: 700;
      &:hover {
        opacity: 1;
      }
      a {
        display: flex;
        align-items: center;
        height: 100%;
        width: 100%;      
      }      
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，菜单中所有的 ul 和 li 标签都将继承上面的样式。我们还需要一个箭头来标记有下拉菜单的项目。

但是用 CSS 术语来说，我们如何知道哪个条目有下拉菜单，哪个没有呢？

我们简单的加一个班不同于李的。当我们只有一个

*   , it is a drop-down menu. When we have one with a "."*   . Link "class, which is a link.

知道了这一点，我们可以这样给李加上箭头:

```
.menu {
  // ...
  ul {
    // ...
    // lets put an arrow down 
    // to the li`s with dropdown
    li {
      padding-right: 36px;
      &::before {
        content: '';
        width: 0; 
        height: 0; 
        border-left: 5px solid transparent;
        border-right: 5px solid transparent;
        border-top: 5px solid #FFA500;
        position: absolute;
        right: 8px;
        top: 50%;
        transform: translateY(-50%);
      }
    }
    .link {
      // links dont need arrow
      &::before {
        padding-right: 0;
        display: none;
      }      
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

要理解我们是如何只用 css 创建一个箭头的，你可以阅读这篇关于 css-tricks 的文章:[https://css-tricks.com/snippets/css/css-triangle/](https://css-tricks.com/snippets/css/css-triangle/)。

## 菜单

第一个

```
.menu {
  // ...
  // the first ul inside the container
  // is the menu, so must be visible 
  // and have its own style
  > ul {
    display: flex;
    height: var(--menu-height);
    align-items: center;
    background-color: #000000;
    // the first ul elements can be a
    // link or an li with a nested ul. 
    // the nested ul will be a dropdown
    li {
      position: relative;
      margin: 0 8px;
      // the dropdown style
      ul {
        // THE DROPDOWN GOES HERE
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们必须使用>操作符来选择菜单 div 中的第一个 ul 元素。我们需要它来使我们的第一个用户界面看起来像一个菜单，而不会弄乱内部的用户界面。

请注意代码示例中的注释“//下拉列表放在这里”。如果你看一下我们的 html 结构，列表项必须包含一个 ul(下拉菜单)。这个注释准确地显示了我们在 CSS 代码中定位这个下拉列表的位置。

但是首先，让我们把它添加到 html 上

```
<div class="menu">
  <ul>
    <li class="link">
      <a href="">This is a link</a>
    </li>   
    <li>
      Now we have a dropdown
      <ul>
        <li class="link">
          <a href="">Sub A</a>
        </li>
        <li class="link">
          <a href="">Sub B</a>
        </li>        
      </ul>
    </li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 下拉列表

下拉菜单是我们菜单架上列表项目内的每一个

```
// the menu
.menu {
  // the menu holder with options
  > ul {
     // the menu items
     li {
        // the item dropdown
        ul {
        }
     }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

李必须在悬停时显示其

```
.menu {
  // ...
  > ul {
    // ...
    li {
      // the holder
      position: relative;
      margin: 0 8px;
      ul {
        // the dropdown
        visibility: hidden;
        opacity: 0;        
        padding: 0;
        min-width: 160px;
        background-color: #333;
        position: absolute;
        top: calc(var(--menu-height) + 5px);
        left: 50%;
        transform: translateX(-50%);
        transition: 200ms;
        transition-delay: 200ms;
        // the dropdown items style
        li {
          margin: 0;
          padding: 8px 16px;
          display: flex;
          align-items: center;
          justify-content: flex-start;
          height: 30px;
          padding-right: 40px;
          // lets put an arrow right
          // to the inner li`s with
          // dropdowns
          &::before {
            width: 0; 
            height: 0; 
            border-top: 5px solid transparent;
            border-bottom: 5px solid transparent;
            border-left: 5px solid #FFA500;
          }
          // every dropdown after the
          // first must open to the right
          ul {
            top: -2%;
            left: 100%;
            transform: translate(0)
          }
          &:hover {
            background-color: #000000;
          }
        }
      }
      // on hover an li (not an <a>)
      // must show its ul (dropdown)
      &:hover {
        > ul {
          opacity: 1;
          visibility: visible;
          transition-delay: 0ms;
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很长很重要的部分。让我们理解它。

我们选择列表项目中的所有 ul，并将其转换为下拉列表。要实现它，我们必须遵循以下步骤:

1.  开始告诉 CSS 如何绘制嵌套的 ul 以及放置它的位置。首先，这个 ul 开始是不可见的，当我们将鼠标悬停在它的父级上时，它就会显示出来。当可见时，下面的代码将负责把所有的放在正确的地方:

```
// ...
position: absolute;
top: calc(var(--menu-height) + 5px);
left: 50%;
transform: translateX(-50%);
transition: 200ms;
transition-delay: 200ms; 
```

Enter fullscreen mode Exit fullscreen mode

顶部会将下拉菜单设置在导航栏之后，这就是我们设置菜单高度变量的原因。

left +转换将使下拉菜单相对于其父菜单居中。过渡会使它平滑，延迟会给光标一个在下拉菜单间导航的间隔。

1.  包含下拉列表(ul)的列表项必须是相对的。所以我们可以给下拉菜单一个绝对位置，并使它的位置相对于它的父节点。在代码中，这个 li 有一个 flex 样式来集中它的内容。

2.  有一点很棘手:我们将在第一级之后覆盖下拉列表的位置。那是因为在那之后，下拉菜单必须偏向右边。

如果你想让下拉列表趋向底部，你可以忽略这里的“左”选择器。但是你不能忽略顶部的属性，否则你的下拉列表会重叠。

这是为嵌套的下拉菜单提供正确位置的代码部分:

```
// every dropdown after the first level
// must pair the top with its parent, 
// and must tend to the right
ul {
  top: -2%;
  left: 100%;
  transform: translate(0)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将在悬停其父级时显示下拉列表:

```
// when hover an li (not an <a>)
// must show its ul (dropdown)
&:hover {
  > ul {
    opacity: 1;
    visibility: visible;
    transition-delay: 0ms;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 搞定

最后，你必须有下面的代码:

```
.menu {
  // define the height of the menu
  --menu-height: 40px;
  // holder and ul general style
  box-sizing: border-box;
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  ul {
    list-style: none;
    padding: 16px;
    margin: 0;
    li, li a {
      opacity: .8;
      color: #ffffff;      
      cursor: pointer;
      transition: 200ms;
      text-decoration: none;
      white-space: nowrap;
      font-weight: 700;
      &:hover {
        opacity: 1;
      }
      a {
        display: flex;
        align-items: center;
        height: 100%;
        width: 100%;      
      }      
    }
    // lets put an arrow down 
    // to the li`s with dropdown
    li {
      padding-right: 36px;
      &::before {
        content: '';
        width: 0; 
        height: 0; 
        border-left: 5px solid transparent;
        border-right: 5px solid transparent;
        border-top: 5px solid #FFA500;
        position: absolute;
        right: 8px;
        top: 50%;
        transform: translateY(-50%);
      }
    }
    .link {
      // links dont need arrow
      &::before {
        padding-right: 0;
        display: none;
      }      
    }
  }
  // the first ul inside the container
  // is the menu, so must be visible 
  // and have its own style
  > ul {
    display: flex;
    height: var(--menu-height);
    align-items: center;
    background-color: #000000;
    // the first ul elements can be a
    // link or an li with a nested ul. 
    // the nested ul will be a dropdown
    li {
      position: relative;
      margin: 0 8px;
      // the dropdown style
      ul {
        visibility: hidden;
        opacity: 0;        
        padding: 0;
        min-width: 160px;
        background-color: #333;
        position: absolute;
        top: calc(var(--menu-height) + 5px);
        left: 50%;
        transform: translateX(-50%);
        transition: 200ms;
        transition-delay: 200ms;
        // the dropdown items style
        li {
          margin: 0;
          padding: 8px 16px;
          display: flex;
          align-items: center;
          justify-content: flex-start;
          height: 30px;
          padding-right: 40px;
          // lets put an arrow right
          // to the inner li`s with
          // dropdowns
          &::before {
            width: 0; 
            height: 0; 
            border-top: 5px solid transparent;
            border-bottom: 5px solid transparent;
            border-left: 5px solid #FFA500;
          }
          // every dropdown after the
          // first must open to the right
          ul {
            top: -2%;
            left: 100%;
            transform: translate(0)
          }
          &:hover {
            background-color: #000000;
          }
        }
      }
      // on hover an li (not an <a>)
      // must show its ul (dropdown)
      &:hover {
        > ul {
          opacity: 1;
          visibility: visible;
          transition-delay: 0ms;
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的 css 将允许一个像这样的 html 结构

```
<div class="menu">
   <ul>
     <li>
       Dropdown A
       <ul>
         <li class="link">
           <a href="">Im a link</a>
         </li>
         <li class="link">
           <a href="">Im a link</a>
         </li>
         <li>
           Nested dropdown
           <ul>
             <li class="link">
               <a href="">Im a link</a>
             </li>
             <li class="link">
               <a href="">Im a link</a>
             </li>
           </ul>
         </li>
       </ul>
     </li>
   </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这里的理念非常普通和简单，即使需要时间去理解。每个列表项必须在悬停时显示其子 ul 元素。

让事情变得复杂的是，我们必须为不同级别的下拉菜单改变 css 规则，因为元素会改变它的行为。

第一个 ul 级别是菜单本身，它是一个水平栏。第二个 ul 级别是水平且集中的下拉线。下一个 ul 级别是内部下拉菜单，它们倾向于右边，以此类推。

你可以用 javascript 给下拉菜单添加一个碰撞系统，改变它们的生长方式，或者把所有东西都改成汉堡包菜单，等等。它足够简单，可以很好地扩展。

我尽了最大努力来解释它，但是我不想过多地解释一些东西，这些东西可能是看代码最容易理解的。对于那种东西，一些例子，一个整体的解释，还有 google 作为一个工具，一定足以得到思路，我相信。

希望对初学者有所帮助。请记住:最好的老师是实践:)

谢谢你的时间。所有人都到了。

(封面照片由 Jo Szczepanska 在 Unsplash 上拍摄)