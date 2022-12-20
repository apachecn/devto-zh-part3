# LiveData 示例

> 原文：<https://dev.to/davidsprogrammingadventures/livedata-examples-3cej>

因此，在 Google Codelabs 教程中，提到了将数据从数据库传递到您的活动的实时观察者。向您展示了如何使用 LiveData 的一个版本，即将一个列表包装在 LiveData <>中，但是还有其他的方法和选项吗？我在这里列举了几个例子，都是我在网上找到的。

### 第一个例子

是一个简单的 Livedata，它包装了一个列表或项目，只有当数据库以单独的方式改变时，它才会改变。

[![alt text](img/2c2ec2235da5b5acda23aeba5850f16c.png "default dao livedata list")](https://res.cloudinary.com/practicaldev/image/fetch/s--f4pG3M4v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mlw94m3cm8rdtsuwiuri.JPG)

通过存储库(如果有)传递，然后传递到视图模型:

[![alt text](img/3e9863cfb84da4002ce31f02e74db94a.png "view model class live data")](https://res.cloudinary.com/practicaldev/image/fetch/s--SXceKciJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdhjkpoz50tscohv2nc3.JPG)

然后 viewmodel 在您的 activity 类中初始化，这样您就可以创建 observer 并将其数据传递给数据容器。

[![alt text](img/35e2605d5df543da94ad0319dcd75848.png "live data observer listexample")](https://res.cloudinary.com/practicaldev/image/fetch/s--jGGuukCI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zvyq6q4p4ddwqyv9m92l.JPG)

### 第二个例子

向 LiveData 传递参数

修改刀

[![alt text](img/6e9c207b6edbaef26b4a220de04dee4f.png "dao query with param")](https://res.cloudinary.com/practicaldev/image/fetch/s--DWHXekgK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tu63h5xpo50l5gthdg96.JPG)

在 ViewModel 中，您需要使用第二个 LiveData，称为 MutableLiveData。
这个可变 LiveData 将用于从活动中向原始 LiveData 传递一个查询词。

[![alt text](img/409c74bbcb20976690e4b24d4d68bfa0.png "search term in view model")](https://res.cloudinary.com/practicaldev/image/fetch/s--fQriPUkH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h20jisukla3oazm2y6kg.JPG)

这在活动代码中引起的唯一变化是设置 search_term 的额外调用

[![alt text](img/e2fcd81c000b459c701b50c0e9ee574f.png "activity search term enter")](https://res.cloudinary.com/practicaldev/image/fetch/s--HVTE2vO0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/maw5wse4ydligx5w2adx.JPG)

### 第三个例子

将 LiveData 传入 PagedList。使用 android architecture Room 从 sql 创建 Datasource.factory 方法。这个方法是填充页面列表的好方法，我只学了一点点，因为在我第一次学习 Android 的时候，我把它误认为是一个页面适配器。

首先修改您的 DAO 以使用数据源。

[![alt text](img/192d06a4354ec932830577ff0b31cb43.png "Datasource dao example")](https://res.cloudinary.com/practicaldev/image/fetch/s--Ab4LIs0A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7mruvkbgle0y827tpbjr.JPG)

在 Viewmodel 类中，您设置了分页列表来与 LiveData 交互。

[![alt text](img/5802106560e755f290d821ce1b206330.png "view model paged list and datasource")](https://res.cloudinary.com/practicaldev/image/fetch/s--KglRhl23--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ajlq76uqwj8opnv8580h.JPG)

最后，设置一个观察器，并从 pagedList 向适配器传递工厂数据，这基本上与示例 1s 相同。

[![alt text](img/40c23be53414265df62db4a23a2cf584.png "submit words to adapter example datafactory")](https://res.cloudinary.com/practicaldev/image/fetch/s--MZkjtiz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p0u36nolohorr9y8e61u.JPG)

虽然示例 1 和示例 2 通常可以正常工作，但是根据您的数据，示例 3 可能更难实现，但是目前这是我能写出的最简单的示例。没有通常与页面列表相关联的任何额外内容。