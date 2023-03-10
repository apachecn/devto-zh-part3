# 龙珠

> 原文：<https://dev.to/jamesrweb/an-introduction-to-dragondrop-1ce5>

## 简介

DragonDrop 是一个可访问的拖放库。它允许我们轻松地创建动态的拖放 UI，同时又易于访问。我真的很兴奋这个项目，并想给你一个快速介绍如何获得设置和如何配置模块。

## 设置

为了安装 DragonDrop 包，我们将使用`npm` -节点包管理器。

```
npm i drag-on-drop -S 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们有两种方法将文件链接到我们的项目。第一种方式是通过脚本标签链接，比如:

```
<script src="/node_modules/drag-on-drop/dist/dragon-drop.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

不过我个人更倾向于链接成 ES6 模块。这是我参与的项目中最简单的事情，非常适合正常的构建过程，比如 webpack 或 gulp。要将模块用作导入，请在您的用例的相关 javascript 文件中添加以下内容。

```
import DragonDrop from 'drag-on-drop'; 
```

Enter fullscreen mode Exit fullscreen mode

无论您选择哪种方式，代码都是一样的，无论您选择脚本标记方法还是导入方法，都没有区别。如果您需要支持不支持导入的旧浏览器，请记住使用 webpack 之类的工具编译您的代码。另外，请记住，如果您没有先编译，您应该将您的脚本链接为一个模块，因为这是通过将 type="module "属性添加到您的脚本标签来完成的，例如:

```
<script src="/dist/index.js" type="module"></script> 
```

Enter fullscreen mode Exit fullscreen mode

## 龙珠类

DragonDrop 类的实例化如下:

```
new DragonDrop(container, options); 
```

Enter fullscreen mode Exit fullscreen mode

DragonDrop 构造函数有两个参数。第一个是您希望可拖放的项目的容器。第二个参数是您希望应用于 DragonDrop 的可配置选项，我们将在下一节中看到。

### 默认选项

默认设置如下:

```
const options = {
  item: 'li', // qualified within container
  handle: 'button', // qualified within `item`
  activeClass: 'dragon-active', // added to item being dragged
  inactiveClass: 'dragon-inactive', // added to other items when item is being dragged
  nested: false, // if true, stops propagation on keydown / click events
  announcement: {
    grabbed: el => `Item ${el.innerText} grabbed`,
    dropped: el => `Item ${el.innerText} dropped`,
    reorder: (el, items) => {
      const pos = items.indexOf(el) + 1;
      const text = el.innerText;
      return `The list has been reordered, ${text} is now item ${pos} of ${items.length}`;
    },
    cancel: 'Reordering cancelled'
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 项

这是容器中可拖动项的 CSS 选择器。

#### 手柄

这是用于激活拖放行为的项目的 CSS 选择器。如果设置为`false`，整个`item`将是可拖动的。

#### activeClass

这是在拖动/选择过程中添加到项目的类。

#### 空闲类

这是在删除/取消选择期间添加到项目中的类。

#### 公告

这就是我们如何配置使用辅助技术(如屏幕阅读器或盲文机)为用户阅读的内容。

##### 抓起

当容器中的一个项目被抓取时，该事件被触发

##### 下降了

当容器中的项目被放下时，就会引发这个事件

##### 重新排序

当容器中的项被移动到容器中的新位置时，将触发此事件

##### 取消

当容器中的一个项目被抓取但在放下时不移动时，或者当通过键盘交互抓取并按下 escape 键时，将触发此事件

### 示例设置

一个简单的设置示例如下:

```
import DragonDrop from 'drag-on-drop';

/** 
* Note: '.dnd__list' is a <ul></ul> in my html page
* Note: The 'dnd' in the class name is short for DragonDrop
**/

const container = document.querySelector('.dnd__list');
const options = {
  item: 'li',
  handle: false,
  activeClass: 'dnd__active',
  inactiveClass: 'dnd__inactive'
};
new DragonDrop(container, options); 
```

Enter fullscreen mode Exit fullscreen mode

#### 分解下来

*   我们选择了一个 DragonDrop 容器，它上面有一个自定义类
*   列表中的每个`<li></li>`都是可拖动的项目，因为没有设置句柄
*   有一个自定义类，当一个项目被拖动、移动或放下时，我们可以用它来设置`item`的样式

很简单，对吧？

## 结论

这是一个非常酷的项目，事实上，如果使用得当，这是一个容易实现的项目，当需要时，这是一个很好的工具。如果你想了解更多，请查看 Github 上的 [DragonDrop 库。](https://github.com/schne324/dragon-drop)

如果你想让我写关于这个项目的更深入的信息或者写一些例子，请随时告诉我，我会看看我能做什么！