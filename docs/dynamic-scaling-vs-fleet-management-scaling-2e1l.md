# 动态扩展与车队管理扩展

> 原文：<https://dev.to/victorcosta/dynamic-scaling-vs-fleet-management-scaling-2e1l>

Aws 自动扩展分为两类:车队管理和动态扩展。

* * *

### 车队管理

用于:

*   替换不健康的实例；
*   在可用性区域中分布实例以最大化弹性；例如:您在美国东部运行实例，因此自动扩展可以在以下 az 中提供实例:美国东部-1a、美国东部-1b、美国东部-1c、美国东部-1d 和美国东部-1e；

* * *

### 动态缩放

用于:

*   当达到阈值时，基于 cloudWatch 警报指标或指标类型(稍后将详细介绍)进行扩展，或者根据违反 cloudWatch 警报阈值的情况采取不同的措施。

#### 动态缩放的类型

*   简单的扩展:基于单个 cloudwatch 警报指标进行扩展，并应用您定义的度量；
*   分步扩展:根据不同级别的云观察警报指标进行扩展，并应用您定义的操作；
*   目标跟踪缩放:基于度量类型进行缩放，但是将要采取的动作委托给 AWS

* * *

#### 用哪个？

这个问题不该问。实际上，您将使用开箱即用的**车队管理**，有可能配置**动态扩展**来采取一些定制的行动；

* * *

### 我们来看一些例子:

> 要获得自动缩放配置，您应该转到`EC2`仪表板，并在侧边栏中找到`Auto Scaling Groups`。选择一个自动缩放组，并在自动缩放组列表下方的选项卡中找到名为`Scaling Policies`的组

**机群管理**:EC2 实例上运行的应用程序停止响应健康检查，然后自动扩展停止向其路由流量，并将该实例移动到隔离区进行分析，并启动另一个实例来替换它；

**动态扩展——简单的扩展**:您有一个 cloudWatch 警报，它监视 EC2 实例的 cpu 利用率，并在超过 80%持续 300 秒(5 分钟)时发出警报。您的**简单扩展策略**定义了要采取的行动是再加速 1 个实例。

[![Simple scaling policy](img/8194e4c795de1dea4fb77ab4fd56e6bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3_nx5zLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0sys39f0994rrake8oox.png)

**动态缩放-步进缩放**:

您有一个 cloudWatch 警报，它监视 EC2 实例的 cpu 利用率，并在 300 秒(5 分钟)内超过 50%时触发警报。您的**步骤扩展策略**定义了要采取的行动:

*   当 cpu 利用率< = 50%且< 60%时，再加速 1 个实例；
*   当 cpu 利用率< = 60%且< 70%时，再加速 3 个实例；
*   当 cpu 利用率< = 70%且

请记住，这些实例将会增加，所以如果您的 cpu 利用率逐渐增加到 70%,您将最终拥有 9 个 EC2 实例；

[![Step scaling policy](img/536cef0e671b59fbbfc759a1d89457a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S5ogN9-N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0vcoj23wmy5hyoh9ljt8.png)

**目标跟踪扩展** :
您希望将您的设备群的 cpu 利用率保持在 50%，但是让 AWS 来处理应该启动或终止多少实例以保持该指标。

注意:Aws 运行算法，并根据接收需求定义如何最好地采取措施来扩展/纳入 EC2 实例。

**公制类型**:

*   每个目标的应用程序负载平衡器请求计数；
*   平均 CPU 利用率；
*   平均网络(字节)；
*   平均网络输出(字节)；

[![Target tracking scaling policy](img/f025e9283d82ffd48bf94b1f6ff9ba49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z3mPLZaB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mq4s5xmg3lxl4dlh702z.png)

* * *

让我知道这是否在下面的评论中全部清楚或者有一个反应。

* * *

### 参考文献:

*   [https://AWS . Amazon . com/solutions/server-fleet-management-at-scale/](https://aws.amazon.com/solutions/server-fleet-management-at-scale/)
*   [https://AWS . Amazon . com/blogs/compute/fleet-management-easy-with auto-scaling](https://aws.amazon.com/blogs/compute/fleet-management-made-easy-with-auto-scaling)
*   [https://docs . AWS . Amazon . com/auto scaling/ec2/user guide/as-scale-based-on-demand . html](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html)
*   [https://www.youtube.com/watch?v=WUUbOQyrnJU](https://www.youtube.com/watch?v=WUUbOQyrnJU)
*   [https://www.youtube.com/watch?v=srofVz6xvkE](https://www.youtube.com/watch?v=srofVz6xvkE)