# 教程:android 架构蓝图，完整 todo 应用(MVO 版)

> 原文：<https://dev.to/erdo/tutorial-android-architecture-blueprints-full-todo-app-mvo-edition-259o>

*这是 android 系列的一部分[fore](https://erdo.github.io/android-fore/)T3】*

| 系列教程 |
| --- |
| 1)教程:[发现故意的 bug](https://dev.to/erdo/tutorial-spot-the-deliberate-bug-165k) |
| 2)教程: [Android fore 基础知识](https://dev.to/erdo/tutorial-android-fore-basics-1155) |
| 3)教程: [Android 架构，全 todo app (MVO 版)](https://dev.to/erdo/tutorial-android-architecture-blueprints-full-todo-app-mvo-edition-259o) |
| 4)教程:[安卓状态诉事件](https://dev.to/erdo/tutorial-android-state-v-event-3n31) |
| 5)教程:[科特林协程，改型和前代](https://dev.to/erdo/tutorial-kotlin-coroutines-retrofit-and-fore-3874) |

[Android 架构蓝图](https://github.com/googlesamples/android-architecture) *通过多次实现相同的待办事项类型应用程序来展示不同的 Android 架构(每个架构变体一次)。

**请注意，Android Architecture blue prints repo 现在已经放弃了 todo 应用程序的原始版本 1——这里的 MVO 实现仍然是代码最少的 Java 实现，远远超过其他实现。*

我留下这篇文章，因为它提供了一个很好的指导，告诉我们如何以及为什么可以从 MVP 架构转向像 MVVM 这样更受状态驱动的东西，这篇文章更进一步，实现了一个完整的 MVO 风格的项目(这就是代码数量如此显著下降的原因)。如果读完这篇文章后，你想在 kotlin 中找到一些东西，那么 [clean architecture](https://dev.to/erdo/clean-architecture-minus-reactive-streams-10i3) 的帖子是接下来的好去处——或者只是看看现有的非常简单的 fore sample 应用程序，比如带有 [persista 库](https://github.com/erdo/persista/tree/main/example-app)T5 的那个

在这篇文章中，我们将另一种架构融入其中: [MVO](https://erdo.github.io/android-fore/00-architecture.html#shoom) 用 [fore](https://erdo.github.io/android-fore/) 实现。

<figure>

[![screen shots of the todo app](img/23590ceb9bc90412547fe2cedeb253be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nL2zaTab--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h4tr9itybrd9zdtlo8lq.png)

<figcaption>screen shots of the MVO todo app</figcaption>

</figure>

我们的 fork 是用 Java 编写的，它基于参考 MVP 实现。

*   它使用比任何其他 Java 实现都少的代码(1971 行，3261 行公司测试)
*   Kotlin-MVO 版本将使用更少
*   许多剩余的代码已经被移出了视图层
*   可以说，这款应用的结构清晰了许多。

虽然这不是一个完美的比较:我们的 MVO 版本以不同的方式处理模拟服务器，并且我们为 DI 添加了一个基本的 Dagger 2 实现(尽管我们在那里留下了一个纯 DI 实现类，以便您可以比较这两者)。我们的 MVO 版本实际上比原来的版本多了*功能，我们使用 DiffUtil 支持动态列表更改，并且有一个健壮的网络实现来从后端获取任务(一些 json 托管在[mocky . io](https://www.mocky.io/)-**)，尽管如此，它仍然使用较少的代码**。*

 *让我们看看我们是如何做到的...

*(注意:在下面的代码中有很多对**任务**的引用，在这种情况下，任务意味着现实世界中的物理任务，就像家务杂事或待办事项。与 Android Task 或 AsyncTask 无关。)*

* * *

## 原始 MVP 包结构

<figure>

[![original mvp package structure](img/43badfbb9a555df952a66d9e57ad4026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5qSWETc8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/88w3z1nf5fh97l64emhs.png)

<figcaption>original package structure</figcaption>

</figure>

MVP 版本的包结构稍微混合了一些概念:**数据**和 **util** 是不言自明的，但是 **addedittask** 、**统计数据**、**任务明细**、**任务**都引用了应用程序 UI 的部分，(它们映射到添加/编辑屏幕、统计屏幕等等)。

<figure>

[![mvp example ui package contents](img/848075efe8438b87a377a64364993b68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FE_LU1A_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/98a0vz6jbxeueuke8s6u.png)

<figcaption>view layer package contents</figcaption>

</figure>

事实上，在这些 UI 包中，我们看到了 android UI 的东西，如活动、片段，以及通常的 MVP 类:契约和演示者。

整体结构使它看起来好像整个应用程序是它的用户界面(加上数据和一些实用方法)。就将视图层与应用程序的其余部分分离而言，我们可以做得比这好得多。

(*演示者是为他们驱动的特定视图编写的，但在大多数应用中**即使是这个小应用**视图也是同一数据的不同窗口，演示者类中发生的许多事情在多个演示者中重复。MVO 所做的事情之一就是将这些代码移动到离应用程序更近的地方，而远离可能会使用它的特定视图。这意味着它可以编写和测试一次，极大地改善了 DRY，并且仍然支持多个更薄的视图，这些视图更容易更改。*)

## MVO 包结构

<figure>

[![new mvo package structure](img/48a41b0072aa87249a902ed82e9e2454.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rn6yr57a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s2th1unloyhmgl65ypzf.png)

<figcaption>new package structure</figcaption>

</figure>

对于 MVO 实现，我们将*数据*包分成了 **api** 和 **db** (将这两个包分开很有用，例如，它可以让我们处理较小的 api 变化，而不会对我们的 db 模型产生太大影响)。

我们将让 **util** 包保持原样——它不是我们讨论的中心。

**消息**包含我们的全局适用的应用程序消息，如 ERROR_BUSY 或 ERROR _ SESSION _ TIMED _ OUT——我们不希望 api 包之外的任何东西知道类似 HTTP 错误代码或网络异常之类的事情。api 包中的代码为我们处理 HTTP 和这些消息之间的映射，而不会让任何网络细节泄露到应用程序的其他部分。

在我看来，上面的一切都是好主意，但不是专门针对 MVO 的，接下来的两个包才是真正定义 MVO 结构的: **ui** 和**特性**...

### ui 包

<figure>

[![new mvo view layer packages](img/61c611c8d90e83e595b133b4ee64a772.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--66vk-6f---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pmpl7dkr1kqvcikikkog.png)

<figcaption>view layer packages</figcaption>

</figure>

在 **ui** 包中，你会发现 **addedit** 、 **statistics** 、 **taskdetail** 和 **tasks** 子包，它们映射到应用程序的屏幕上。这里的一切都与用户界面密切相关，因此 Android 框架，这里的代码很难测试，速度很慢，所以我们想尽可能使它变得简单。因此，在这里您会发现 Activity 和 Fragment 类，偶尔还有适配器，或者任何其他与特定视图直接相关的类。不过，这里不需要演示者或合同类。

### 功能包

现在是**特性**包。如果你认为应用程序是独立存在的，不涉及任何特定的用户界面，那么功能包就是它的主要部分。如果你想用干净的架构来命名事物，这就是你所说的领域层。

这个小 app 只有一个“功能”——todo/任务管理，所以这里只有一个子程序包: **tasks** 。大多数商业应用程序都会有更多的功能，典型的例子有:*账户*、*购物篮*、*收藏夹*、*聊天*、*忠诚度*、*收件箱*、*播放列表*等。

这是 [MVO](https://erdo.github.io/android-fore/00-architecture.html#shoom) 中[模特](https://erdo.github.io/android-fore/02-models.html#shoom)居住的地方。这是应用程序的业务逻辑，这里的代码应该易于单元测试:这些类应该尽可能少地了解 Android、上下文，当然也不了解片段生命周期等。关于如何编写这些模型的完整指南，你可以参考[fore docs](https://erdo.github.io/android-fore/02-models.html#writing-a-basic-model)——这是标准建议，也适用于编写视图模型。

* * *

## 任务特征

<figure>

[![tasks feature main classes for the MVO implementation](img/504cb026833525912534cdaa786d11b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P3ASj8SO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/apkp48w2xpr05atjhkd2.png)

<figcaption>tasks feature, MVO implementation</figcaption>

</figure>

这是对 MVP 实现中存在的代码的实质性重写。

### TaskItem

这是应用程序对任务的定义。

*   看起来很像，但是和我们为了不混淆而故意称之为 [**TaskItemPojo**](https://github.com/erdo/android-architecture/blob/todo-mvo/todoapp/app/src/main/java/com/example/android/architecture/blueprints/todoapp/api/tasks/TaskItemPojo.java) 的 **api** 包中的任务类不一样。

*   它看起来很相似，但与我们故意称之为 [**TaskItemEntity**](https://github.com/erdo/android-architecture/blob/todo-mvo/todoapp/app/src/main/java/com/example/android/architecture/blueprints/todoapp/db/tasks/TaskItemEntity.java) 的 **db** 包中的任务类并不相同，这样我们就不会混淆了。

*(这可能看起来很费力，如果你愿意，你可以编写一个单一的任务类来满足你的 api、你的数据库模型和你的特性的所有需求——当你有变化的需求和 API 时，事情会变得复杂，所以要注意这里的权衡)*

以下是该功能的其余部分:

<figure>

[![tasks feature](img/3daeba45fd699d2d2f9aff8bfc85dd6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JWORattU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sox9itpxdxxiwuqrctgv.png)

<figcaption>tasks feature</figcaption>

</figure>

### 任务教师

所有这些都是:连接到网络，从后端获取任务，并将它们添加到本地存储。

大量与连接网络相关的工作:解析响应；处理错误；穿线；etc 是由 fore 的 [CallProcessor](https://erdo.github.io/android-fore/04-more-fore.html#fore-network) 处理的，它是一个薄薄的包装器，覆盖了 Retrofit 和 ok http(Apollo 和 Ktor 也各有一个)。我们将下载的任务直接传递给处理数据库工作的**任务列表模型**。所以这门课留给我们的只有一点商业逻辑。

它是**可观察的**，因此它将让任何观察者知道它的状态何时改变(例如，isBusy()何时切换回 false)。

完整的代码是[这里是](https://github.com/erdo/android-architecture/blob/todo-mvo/todoapp/app/src/main/java/com/example/android/architecture/blueprints/todoapp/feature/tasks/TaskFetcher.java)。

### TaskListModel

这个类包装了数据库并负责所有的线程，因此视图层不需要担心这个问题。对数据库的任何访问都要经过这里，所以数据库对应用程序的其余部分是透明的。

这个类被设计成支持 Android 适配器，所以它包括像 **size()** 和 **get()** 这样的公共方法。

这个类也是**可观察的**，所以任何观察者都会被告知任务列表的任何变化。当我们包装一个 Room db 时，我们简单地挂钩到 Room 自己的 **InvalidationTracker** 中，这直接类似于 fore 的观察者。

**动画列表更新**:因为我们计划将任务列表的变化动画化，所以我们在这里实现了 Diffable 接口(这是来自 **fore** 的一个小助手，它让我们自动化了使用 DiffUtil 的大部分复杂性)。

完整的代码是[这里是](https://github.com/erdo/android-architecture/blob/todo-mvo/todoapp/app/src/main/java/com/example/android/architecture/blueprints/todoapp/feature/tasks/TaskListModel.java)。

### CurrentTaskModel

该类驱动与特定任务相关的任何视图(当前为 **taskdetail** 和 **addedit** )。

为此，它有一些公共方法，如 **setTitle()** 、 **getDescription()** 、 **saveChanges()** 等

与其他模型一样，它是在假设所有方法都将在同一个线程上被调用的情况下编写的。任何线程都是在远离视图层的内部进行管理的。

同样，它是可观察的，因此任何观察者都知道何时同步他们的视图。

* * *

## 固定视图层

现在我们开始简单的部分了！看看旧视图层代码中存在的一些 set / show 方法:

```
@Override
public void setLoadingIndicator(final boolean active) {
  if (getView() == null) {
    return;
  }
  final SwipeRefreshLayout srl =
    (SwipeRefreshLayout) getView().findViewById(R.id.refresh_layout);

  // Make sure setRefreshing() is called after the layout is done with everything else.
  srl.post(new Runnable() {
    @Override
    public void run() {
      srl.setRefreshing(active);
    }
  });
}

@Override
public void showTasks(List<Task> tasks) {
  mListAdapter.replaceData(tasks);
  mTasksView.setVisibility(View.VISIBLE);
  mNoTasksView.setVisibility(View.GONE);
}

@Override
public void showNoActiveTasks() {
  showNoTasksViews(
    getResources().getString(R.string.no_tasks_active),
    R.drawable.ic_check_circle_24dp,
    false
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

即使对于简单的 UI，像这样的代码也会变得非常复杂，并且会在 UI 中引起微妙的、难以发现的错误。这里是支持主要任务 UI 所需的方法的完整列表(注意这些只是方法签名，我们甚至没有显示完整的方法，这些方法在最初的实现中运行了一百多行代码):

```
@Override
public void setLoadingIndicator(final boolean active) {...}

@Override
public void showTasks(List<Task> tasks) {...}

@Override
public void showNoActiveTasks() {...}

@Override
public void showNoTasks() {...}

@Override
public void showNoCompletedTasks() {...}

@Override
public void showSuccessfullySavedMessage() {...}

private void showNoTasksViews(String mainText, int iconRes, boolean showAddView) {...}

@Override
public void showActiveFilterLabel() {...}

@Override
public void showCompletedFilterLabel() {...}

@Override
public void showAllFilterLabel() {...}

@Override
public void showAddTask() {...}

@Override
public void showTaskDetailsUi(String taskId) {...}

@Override
public void showTaskMarkedComplete() {...}

@Override
public void showTaskMarkedActive() {...}

@Override
public void showCompletedTasksCleared() {...}

@Override
public void showLoadingTasksError() {...} 
```

Enter fullscreen mode Exit fullscreen mode

MVO syncView()约定将让我们删除所有这些方法。如果您不习惯 syncView()约定，这可能会有点令人震惊，但实际上上面的所有内容都可以替换为:

```
@Override
public void syncView() {
  tasksView.setVisibility(taskListModel.hasVisibleTasks() ? View.VISIBLE :View.GONE);

  noTasksView.setVisibility(taskListModel.hasVisibleTasks() ? View.GONE :View.VISIBLE);
  noTaskMsg.setText(taskListModel.getCurrentFilter().noTasksStringResId);
  noTaskIcon.setImageDrawable(getResources().getDrawable(taskListModel.getCurrentFilter().noTasksDrawableResId));
  noTaskAddView.setVisibility(taskListModel.hasVisibleTasks() ? View.GONE : View.VISIBLE);
  filteringLabelView.setText(getResources().getString(taskListModel.getCurrentFilter().labelStringResId));
  swipeRefreshLayout.setRefreshing(taskFetcher.isBusy());

  listAdapter.notifyDataSetChangedAuto();
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们完成所有视图时，我们已经删除了许多不必要的代码。

syncView()约定的威力在这里详细讨论[。如果你熟悉 MVI，它和 render()函数有相似的用途。](https://erdo.github.io/android-fore/01-views.html#syncview)

### 动画列表变化

您是否发现了**notifydatascethangedauto()**？(而不是更常见的 notifyDataSetChanged()) -这是 fore 支持动态列表更改的方式，在这种情况下，它由 Android 的 DiffUtil 支持，但还有另一个更高性能的版本，你可以使用它来演示一个简单的内存列表[这里](https://erdo.github.io/android-fore/#fore-3-adapter-example)。无论哪种方式，都是从 syncView()中对 notifyDataSetChangedAuto()的简单调用。

在这一点上，你可能会想 **fore** 一定是某个巨大复杂的库来支持这一切，实际上它很小(就像 500 行代码一样小)。很多动力来自于 MVO 概念本身。

### 旋转支持

<figure>

[![gif showing the app rotating](img/d59505173e35f8f9af899adc7c9128cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u4WJ7tnb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vfb6sq68yym50ihvgheo.gif)

<figcaption>rotation support as standard</figcaption>

</figure>

如果**旋转支持**和**可测试性**没有标配，那就不是 MVO 了。

* * *

## 测试

一些最初的测试不需要修改就可以工作，一些被调整了，其他的不得不重写。MVO 应用程序的测试更侧重于特性包，并使用普通的 JUnit 测试。但是仍然有大量的 Android UI 测试(对于这些测试，我们使用 Dagger2 TestAppModule 来模拟驱动视图层的模型——但是 PureDI 解决方案也同样适用)

* * *

## 事情还没有真正改善...

我们的视图层现在看起来薄了很多，但是我们仍然需要在 activity 类中有相当多的模板来支持 ActionBar、NavigationView、PopUpMenu 和处理 options 菜单。不幸的是，这就是 Android 的设计方式——我们能在多大程度上避免这些原生类和随之而来的锅炉板是有限度的。

单一活动应用程序和谷歌的[导航组件](https://developer.android.com/topic/libraries/architecture/navigation/) *可能*会提供一条出路，或者[可能不会](https://proandroiddev.com/why-i-will-not-use-architecture-navigation-component-97d2ad596b36)。(如果你是 Android 开发新手，你会很快学会对谷歌的建议持保留态度。他们只是试图解决问题，就像我们其他人一样——有时会有帮助，有时没那么有帮助。)

MVO 的好处在于它从视图层移除了如此多的代码，完全重写视图层(例如使用新的导航结构)并不困难，而且几乎不需要接触应用程序的其余代码。

* * *

感谢阅读！如果你正在考虑在你的团队中使用 fore，fore 文档包含了易于理解的示例应用中的大部分基础知识，例如[适配器](https://erdo.github.io/android-fore/#fore-3-adapter-example)、[网络](https://erdo.github.io/android-fore/#fore-4-retrofit-example)或[数据库](https://erdo.github.io/android-fore/#fore-6-db-example-room-db-driven-to-do-list)。

这是我们 MVO 分支的[完整代码](https://github.com/erdo/android-architecture)。*