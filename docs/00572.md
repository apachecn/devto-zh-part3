# TB 大菜单实现及演示(第 2 部分)

> 原文：<https://dev.to/innoraft12/tb-mega-menu-implementation-with-a-demonstration-part-2-aph>

在这篇博客的第一部分，我们已经完成了安装，并为自定义块类型创建了块。在这一部分中，我们将了解如何配置 TB mega 菜单，以便按照我们的设计准备好菜单的结构。

配置 TB mega 菜单和将 TB mega 菜单块放置在一个区域中是两件不同的事情。

TB mega 菜单可针对现有菜单进行配置。因此，在开始配置之前，您需要确保准备好完整的菜单层次结构。但是，您可以稍后从 TB mega 菜单配置页面修改链接。

定义菜单结构

如果你已经有了一个想要使用的菜单，那么你已经领先一步了！如果没有，那么转到/admin/structure/menu 并点击 Add 菜单。像平常一样创建菜单。

出于演示目的，我们将使用下面的菜单结构。假设此菜单的名称为示例菜单:

父母 1

```
-Child (1)

    -Child (1) content

-Child (2)

    -Child (2) content

    -Sub child (1)

        -Sub child (1) content (This content will be displayed over the child (2) content)

    -Sub child (2)

        -Sub child (2) content (This content will be displayed over the child (2) content)

-Child (3)

-Child (4) 
```

父母 2

```
-Child (1)

-Child (2)

    -Sub child (1)

        -Sub child (1) content (This content will be displayed over the child (2) content)

    -Sub child (2)

        -Sub child (2) content (This content will be displayed over the child (2) content)

-Child (3) 
```

父母 3

```
-Child (1)

-Child (2)

-Child (3) 
```

来源原文:[点击这里！](https://www.innoraft.com/blogs/tb-mega-menu-implementation-demonstration-part-2)

第一部分链接:[https://www . inn raft . com/blogs/TB-mega menu-implementation-bean-module-part-1](https://www.innoraft.com/blogs/tb-megamenu-implementation-bean-module-part-1)