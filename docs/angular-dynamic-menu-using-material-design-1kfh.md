# 使用材料设计的角度动态菜单

> 原文：<https://dev.to/tt0686/angular-dynamic-menu-using-material-design-1kfh>

我想创建一个动态菜单，例如，从数据库中提取数据。这是 https://stackblitz.com/edit/angular-dmenu 的项目

在 StackBlitz 项目的 dynamic-menu.component.html 中，有动态解决方案和静态解决方案(这是注释)。

静态解决方案应该是动态解决方案在执行后的呈现方式。
如果我只是运行静态解决方案(取消注释并注释了动态)，一切都会按预期运行，但如果我尝试使用动态解决方案，菜单不会按预期运行(主按钮相互重叠，子菜单无法正常显示和运行)。
这是否与 ng-template 如何影响 CSS 样式有关？
浏览 Chrome 开发者工具，我可以看到动态版本中的 class = " mat-menu-item ng-star-inserted "属性被添加到按钮中，而这在静态版本中不会发生。我已经尝试了一切，但我不能得到它，为什么会发生这种情况。我该怎么解决这个问题。我是新的角度，但我想真正了解框架如何工作

预先感谢

致敬