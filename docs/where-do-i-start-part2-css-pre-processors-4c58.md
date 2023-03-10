# 我从哪里开始？第 2 部分:CSS 预处理程序

> 原文：<https://dev.to/kevindsteeleii/where-do-i-start-part2-css-pre-processors-4c58>

## 上次...

上次我在 **[part-0](https://dev.to/kevindsteeleii/where-do-i-start-part-0-bash-terminal-git-and-github-version-control-3287)** 中发布了一些你在 **[part-1](https://dev.to/kevindsteeleii/where-do-i-start-part1-ides-html-css--css-frameworkslibraries-3acb)** 中做任何事情之前可能想要开始做的事情。我们首先假设您已经很好地掌握了 HTML & CSS，您可以使用文本编辑器，并且知道足够多的 git 和命令行命令，可以在不使用 UI 的情况下完成您需要的大部分工作。

## 我知道 HTML，CSS，还有那些，现在呢？

所以，如果你有这种感觉，你可能已经做了以下的一些事情:

1.  做了一个静态网站
2.  学会移动优先或反应式风格
3.  从事过 **[自由代码营响应式网页设计项目](https://learn.freecodecamp.org/responsive-web-design/responsive-web-design-projects/)** 或类似的项目。

如果没有，你可以从免费代码营的这些 **[HTML](https://learn.freecodecamp.org/responsive-web-design/basic-html-and-html5)** 或 **[CSS](https://learn.freecodecamp.org/responsive-web-design/basic-css)** 课程开始，或者使用任何最适合你的学习风格和需求/目标的资源。如果你想把编程作为一种爱好，那也没问题。我将把 **JavaScript** 的帖子留到以后，因为这篇帖子的长度有点长。

## CSS 预处理~~和 JavaScript~~

与过去的 MySpace pages 和 Friendster 相比，现在的 CSS 使得设计页面更加容易。但是，即使你采用惊人的 CSS 架构，如 **[SMACCSS](https://smacss.com/)** 或坚持你的基本 **[BEM](http://getbem.com/)** ，大型或复杂的项目会使生成精简、有效的 CSS 变得更加困难。进入 CSS 预处理器，他们生成 CSS，因此名称和他们有真正整洁的功能。比如:

*   不那么枯燥的语法
*   嵌套选择器简化并减少了类和修饰符的使用
*   有数据类型，您可以使用它们来实现更好的交互
*   带参数的函数
*   控制流
*   还有更多！！

### 我用 SCSS 举个例子

您希望将一个标签相对于其高度完全居中，但它也嵌套在另一个标签中，但父标签的位置是绝对的或相对于其自己的父标签的。我们会怎么做？

```
 /* In CSS */
  div.parent {
    position: absolute;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
  }

  /* Had to write a specific selector */
  div.parent > div.child {
    --child-height--: 35vh;
      position: inherit;
      margin: 0 auto;
      /* Seems like a lot to write just for this */
      margin-top: calc(50vh - calc(var(--child-height--)/2));
      width: 35vw;
      height: var(--child-height--);
  }

  /* In SASS */
  $child-height: 20vh; // reuseable in multiple places

  div.parent {
    position: absolute;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;

    div.child {
      position: inherit;
      margin: 0 auto;
      margin-top: 50vh - $child-height/2; /* Cleaner */
      width: 35vw;
      height: $child-height;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在想象添加更多的标签和样式。你几乎可以预测你的 CSS 文件会变得多么混乱。在 CSS 预处理器出现之前，你必须编写更多的选择器，使用更多的类，而且它缺乏像 SASS 这样的 CSS 预处理器所提供的广泛功能。

### 资源

*   证明文件
    *   [SASS](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)
    *   [小于](http://lesscss.org/#)
    *   [铁笔郎](http://lesscss.org/#)
    *   [发布 CSS](https://postcss.org/)
*   录像
    *   [Sass 设计速成班](https://www.youtube.com/watch?v=roywYSEPSvc&t=5s)
    *   [Traversy Media 的 LESS CSS 预处理教程](https://www.youtube.com/watch?v=YD91G8DdUsw)

### 下次

下一次我们将最终研究 JavaScript。基本语法和控制流的简要概述、推荐读物和其他资源。和往常一样，我是新人，所以我承认我可能会犯错误，并欢迎批评、讨论或建议。谢谢大家！！