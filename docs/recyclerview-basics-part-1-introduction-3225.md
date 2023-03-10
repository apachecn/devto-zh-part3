# Recyclerview 基础知识(第 1 部分):简介

> 原文：<https://dev.to/raulmonteroc/recyclerview-basics-part-1-introduction-3225>

[![](img/e315f24e0fbae31a37aec64e734fda3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GOV1w9Yj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raulmonteroc.com/wp-content/uploads/2019/03/recyclerview-basics-part-1.png)

Recyclerview 取代了 Listview，具有更简单的 API、更好的性能和一些新的额外功能。如果您不知道 ListView 是什么，不要担心，为了理解 recyclerview，您不需要了解它，只需将它视为一个 UI 组件，用于在具有滚动功能的列表中表示相关项目。

## recycler view 的组件

Recyclerview 主要用于一起列出相关元素，但是，您可以对其方式进行显著定制，以使您的用户在您的应用程序中拥有独特的体验。诸如确定元素如何排列在一起、启用每个元素的自定义视图、响应触摸手势等选项可供您使用。

这些选项不能从 Recyclerview 对象本身直接访问，而是为每个特定的配置使用一组相关的对象，从而使代码更清晰并可重用。

在系列课程中，我们将使用 Recyclerview 的最相关组件，但现在，我将留下一些描述，作为我们接下来将使用的内容的线索，它们是:

#### 取景框

view holder 对象是 Recyclerview 中元素的静态表示，可从 kotlin 或 java 代码中访问。视图持有者的主要职责是显示带有来自适配器对象的绑定数据的每个元素。

#### 适配器

Recyclerview 不直接处理数据，它只负责在屏幕上显示数据。数据管理是通过一个**适配器**对象来完成的，这个对象反过来使用 ViewHolder 对象作为每个元素数据的容器。适配器有三项任务:

*   充当模型数据和 Recyclerview 对象之间的中介。
*   将提供的数据绑定到每个 ViewHolder 对象。
*   确定应该使用哪个 XML 布局来加载视图持有者。

#### [布局管理器](#layoutmanager)

Recyclerview 负责列表元素的显示，但是*做这件事的方式*被委托给 **LayoutManager** 对象。Android 提供了一些内置的布局管理器，如`LinearLayoutManager`和`GridLayoutManager`，但如果你想为你的 Recyclerview 提供更定制的体验，你也可以提供自己的。

#### ItemTouchHelper

一旦我们设置了适配器并选择了布局管理器，我们就可以让 Recyclerview 响应触摸手势，比如拖动元素或向侧面滑动。出于这些目的，我们使用了 **ItemTouchHelper** 对象。

## 下一步

现在我们已经大致了解了什么是 Recyclerview，组件是什么以及它们能做什么，我们准备开始一个小的编码冒险。

在接下来的文章中，我们将使用 Recyclerview 的 ViewHolder & Adapter 组件创建一个简单的元素列表，将所有这些理论付诸实践。