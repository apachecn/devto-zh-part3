# 用 Bean 模块实现 TB 大菜单(第 1 部分)

> 原文：<https://dev.to/innoraft12/tb-megamenu-implementation-with-bean-module-part-1-3gjj>

**TB 之前的世界巨型菜单**

正如我们所知，Drupal 7 并不总是带有 TB mega 菜单。传统的菜单实现包括编写整个 HTML 并根据需要进行设计，或者与其他 contrib 模块并行使用默认的 drupal 菜单来支持不同的需求，这通常需要几天甚至几周的时间来实现。即使完成后，客户可能会要求你做出改变，事情可能会失控。改变每一个 HTML 元素是一项艰巨的任务！另一种方法可能是使用 Drupal 提供的某些模块来创建菜单。这种方法的问题是，尽管菜单看起来很简单，但要在很短的时间内使其完全可配置将是一项艰巨的任务。

这就是 TB mega menu 拯救世界的作用！

“TB Mega Menu 允许您创建一个具有创新后端用户界面的 Mega Menu，并与 Drupal 核心菜单同步。”drupal.org

TB mega 菜单为用户提供了从零开始构建菜单的完全可配置的方法。今天我们将讨论在 Drupal 7 上的实现。它允许用户使用一个模块创建一个包含图像、文本、视频的大菜单。

尽管这些东西可以通过块插入，但是有一个依赖性。

安装和实现非常简单，但是如果没有仔细配置，某些小问题可能会导致车祸。

原文来源:[点击这里！](https://www.innoraft.com/blogs/tb-megamenu-implementation-bean-module-part-1)

第二部分链接:[https://www . inn raft . com/blogs/TB-mega-menu-implementation-demonstration-part-2](https://www.innoraft.com/blogs/tb-mega-menu-implementation-demonstration-part-2)