# 扩展*ngIf 语法以创建自定义权限指令

> 原文：<https://dev.to/angular/extend-the-ngif-syntax-to-create-a-custom-permission-directive-3ic4>

*所以我们的用例是创建一个指令，它根据我们当前认证用户的权限显示/隐藏页面上的元素。在本文中，我们将介绍一个非常简单的用例，但它可以很容易地扩展并用于实际的生产应用程序中。通过创建这样的指令，我们还将深入了解 Angular 的`ngIf`和`ngFor`指令使用的语法。*

假设我们有一个如下所示的用户对象结构:

```
const aUser = {
    username: 'juristr',
    permissions: [
        'permission1',
        'permission2',
        'permission3'
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们在用户认证时得到的结果。我们现在想要实现的是有一个更加**高级的`*ngIf`指令**，它自动检索关于认证用户的信息，并根据我们定义的权限隐藏/显示我们的应用程序块。

例如，这样一个定制的`*hasPermission`指令可以这样工作:

```
<div *hasPermission="['can_write', 'can_read']">
    Only users with "can_write AND "can_read" permissions can see this.
</div> 
```

Enter fullscreen mode Exit fullscreen mode

通过在我们的组件模板中定义这样的块，如果用户没有权限`can_write`和`can_read`，它将自动隐藏。

很整洁，不是吗😃。阅读完整的博客文章&如何创建这样的自定义指令[这里](https://juristr.com/blog/2018/02/angular-permission-directive/?utm_source=devto&utm_medium=crosspost)