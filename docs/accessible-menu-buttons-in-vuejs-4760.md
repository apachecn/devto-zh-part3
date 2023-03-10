# vue.js 中的可访问菜单按钮

> 原文：<https://dev.to/marcush/accessible-menu-buttons-in-vuejs-4760>

tldr[https://github.com/marcus-herrmann/vue-menu-button](https://github.com/marcus-herrmann/vue-menu-button)

我的方法是首先将单页应用中常见的小部件和场景映射到[accessible-app.com](https://accessible-app.com)(由于这个原因，模态对话框和路由已经是[演示应用](https://vuejs.accessible-app.com)的一部分)。你经常看到的是菜单按钮。[现在海登关于这个](https://inclusive-components.design/menus-menu-buttons)是对的——当谈到菜单按钮时，术语很快就变得模糊了。有一些“菜单”或“下拉菜单”会在点击或悬停时显示导航项目(有时触发实体就是导航项目本身)——还有来自 WAI-ARIA 创作实践 1.1 的[菜单按钮设计模式"](https://www.w3.org/TR/wai-aria-practices/#menubutton)。这个术语的后一个定义将在下面讨论。

[ARIA 菜单按钮章节](https://www.w3.org/TR/wai-aria-practices/#menubutton)列举了两个例子说明它们的意思:

*   导航菜单按钮
*   动作菜单按钮

## 差异

这两种方法的区别在于菜单的内容。导航菜单包含链接:

> 菜单项由 HTML 链接组成，并保持其 HTML 链接行为。也就是说，激活 menuitem 会加载链接目标，并且浏览器的链接上下文菜单和关联的操作都是可用的。

而动作菜单包含动作(咄)。根据我的理解，这些菜单动作与按钮有共同之处(例如，它们不改变 URL)，但是缺少它们的许多优点(例如，监听“Enter”和“Space”键事件，自动成为 tab 键顺序的一部分)。

## 相似之处

在标准导航菜单中，我们经常看到 role="menu "和 role="menu-item "的用法。[在这个上下文中，这些用法都是错误的](http://adrianroselli.com/2017/10/dont-use-aria-menu-roles-for-site-nav.html)，在菜单按钮中却是正确的(再次参见[的咏叹调练习](https://www.w3.org/TR/wai-aria-practices/examples/menu-button/menu-button-links.html))。这导致了导航菜单按钮和动作菜单按钮共享的一般结构:

```
button
 menu
     menu-item
     menu-item
     menu-item 
```

现在我们必须传达这些项目之间的关系。ARIA 要求菜单按钮具有

*   `aria-haspopup="true"`指示按钮打开菜单
*   `aria-controls="IDREF"`指被控制的元素，在我们的例子中是菜单
*   `aria-expanded="true"`当菜单打开时(`aria-expanded="false"`或该属性的完全删除表明菜单关闭

这给了我们下面的按钮和菜单包装的 HTML:

```
<button aria-haspopup="true" aria-controls="the-menu" aria-expanded="false">
    Open me
</button>
<ul id="the-menu" role="menu">
    (menu-item)
    (menu-item)
    (menu-item)
</ul> 
```

我故意省略了菜单项的标记，因为它与我们想要在哪里构建导航菜单按钮或操作菜单按钮的决定有关。

对于导航菜单按钮项目，它是:

```
<li role="none"><a role="menu-item">About Page</a></li> 
```

而对于动作菜单项:

```
<li role="menu-item" tabindex="-1">Print this page</li> 
```

乍一看，这很奇怪。但是当你仔细观察，你会明白为什么这一切都有意义:

*   id 为“the-list”的列表在语义上不再是列表，因为它已经有了`role="menu"`。因此，从今以后，它就是一份菜单
*   菜单希望菜单项作为其子菜单。因此，我们将`li`的语义剥离为 listitem:
    *   当它是一个导航菜单项时，我们将`li`的角色设置为 none，但是给其中的锚元素赋予`menu-item`的角色
    *   当它是一个动作菜单项时，`li`变成一个菜单项。同样，使用 tabindex 属性，我们可以确保以编程方式设置该项的焦点(下面将详细介绍)。

## 键盘辅助功能

现在我们建立了一个菜单——我们基本上承诺用户可以像菜单一样使用它。我说的“使用”是指我们的菜单对以下按键做出反应

*   `ESC` / `Enter`:关闭菜单，将焦点设置到菜单按钮
*   `Up Arrow`:将焦点移到上一个菜单项，或者:如果焦点在第一个菜单项上，将焦点移到最后一个菜单项。
*   `Down Arrow`:将焦点移到下一个菜单项，或者:如果焦点在最后一个菜单项上，将焦点移到第一个菜单项。

## 达到目的的方法

在阅读和研究了 ARIA 创作实践之后，我在 React 和 Vue 中搜索了菜单按钮的实现。我发现了许多漂亮的组件，有着奇特的动画，但是它们都缺少菜单按钮语义和键盘行为。让我们假设这些脚本根本不想成为菜单按钮。

幸运的是，React 有 ReachUI，它的创建者 Ryan Florence *希望*提供一个合适的菜单按钮组件[(在这里找到“MenuButton (Dropdown)”组件)](https://ui.reach.tech/menu-button)。当我查看它的代码示例时，我发现了一些非常有趣的东西——导航和动作菜单按钮的混合:

```
<Menu>
  <MenuButton>
    Actions <span aria-hidden>▾</span>
  </MenuButton>
  <MenuList>
    <MenuItem onSelect={() => alert("Download")}>Download</MenuItem>
    <MenuItem onSelect={() => alert("Copy")}>Create a Copy</MenuItem>
    <MenuItem onSelect={() => alert("Mark as Draft")}>Mark as Draft</MenuItem>
    <MenuItem onSelect={() => alert("Delete")}>Delete</MenuItem>
    <MenuLink
      component="a"
      href="https://reach.tech/workshops"
    >Attend a Workshop</MenuLink>
  </MenuList>
</Menu> 
```

## 我的视图菜单按钮

这是一个有趣的方法，我决定为 Vue，尤其是#accessibleapp 效仿。截至目前，你可以在[vuejs.accessible-app.com](https://vuejs.accessible-app.com/)(“账号”)上看到正在使用的该按钮，并在 GitHub 上找到 vue-menu-button 的[代码。](https://github.com/marcus-herrmann/vue-menu-button)

我从 ReachUI 的组件里借来的东西

*   当你的焦点在按钮元素上，点击`Up Arrow`或`Down Arrow`时，菜单打开，电影分别聚焦到第一个或最后一个菜单项。
*   组件的 API:如果想要一个动作项，可以使用 Reach 中的`<MenuItem />`或者`<menu-item />` my Vue 组件。如果你想放置一个链接(或者导航列表项),你可以在 Reach 中写`<MenuLink component="a" href="https://reach.tech/" ... >`,你可以在我的脚本中使用一个`<menu-link />`组件。额外收获:你可以在里面放一个`<router-link>`，它仍然可以工作！

这是辅助应用的 AccountButton 组件的模板:

```
<template>
  <menu-wrapper>
    <template slot="menu-button"
      >Account
    </template>
    <template slot="menu-content">
      <menu-link>
        <router-link to="/orders">Past Orders</router-link>
      </menu-link>
      <menu-link>
        <router-link to="/settings">My Settings</router-link>
      </menu-link>
      <menu-item @click="doSomething">Clear my Shopping cart</menu-item>
    </template>
  </menu-wrapper>
</template> 
```

不知何故，我意识到我的脚本在选项和配置方面还有待改进(如果你有建议，请投稿！).但我首先想做的是展示如何以语义的方式编写这样一个组件，监听关键事件，提供一个合理的 API，[，例如，如何解决`aria-controls`引用](https://github.com/marcus-herrmann/vue-menu-button/blob/master/src/components/MenuWrapper.vue#L42)。希望我成功了。但是即使我没有，在我的研究过程中，我也让自己沉浸在菜单按钮设计模式中——仅仅这一点就值得努力。