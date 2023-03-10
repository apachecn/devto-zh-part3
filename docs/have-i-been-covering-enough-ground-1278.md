# 我是否已经覆盖了足够的范围？

> 原文：<https://dev.to/saral/have-i-been-covering-enough-ground-1278>

我的目标是每天至少走 10000 步。每天 10，000 步加起来大约是 5 英里，相当于每天锻炼 30 分钟。[疾病控制和预防中心](https://www.cdc.gov/physicalactivity/walking/index.htm) (CDC)建议成年人每周至少进行 150 分钟中等强度的有氧运动。因此，每天 10，000 步算是满足了 CDC 的建议。我的目标是看看我是否达到了每天推荐的运动量。

我一直在用 [Garmin Forerunner](https://buy.garmin.com/en-US/US/p/529988) 235 收集我的步骤数据，我能够使用这个代码库 [Garmin DB](https://github.com/tcgoetz/GarminDB) 下载原始数据。一、样本数据范围为 2018 年 10 月 31 日至 2019 年 3 月 31 日。幸运的是，数据相对干净，清理过程没有消耗我太多的时间。

然而，因为我只需要步骤数据，所以我取了整个数据的一个子集，这样我就可以得到上述时间段内所需的步骤数据。我总共有 153 个样本，但不得不删除第一行，因为那天我拿到了跟踪器，当天没有记录任何数据。

### 体面平均

[![](img/427b2bc64614365baf8361fa510b4031.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FfuWnAkW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/569/1%2AbyguHFiWVDDLnV96RcMgJg.png) 

<figcaption>平均走的步数</figcaption>

在过去的 152 天里，我平均每天走大约 10，741 步。这个平均值让我很高兴，因为它清楚地表明我每天至少走了 10000 步。然而，让我明显担心的是远低于每天 10000 步的数据。10000 步以下的比例是多少？

[![](img/57d897538c7640b72053553bc507fd4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rvBhFpzY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/556/1%2AsGiBZKwgljkdmf68hWlzmQ.png)

<figcaption>2019 年 10 月 31 日至 2019 年 3 月 31 日采取的步骤百分比</figcaption>

在 152 天的时间里，我有 62.5%的时间跑了 10000 多步，而我有 37.5%的时间没有完成。

然而，在该区域的两边都有一些巨大的异常值，使我的平均值偏离了大约 200 步。

[![](img/21d9263b758bae31c0e0d3cddff550e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KddBJAVc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/575/1%2AnPdf10AfE2vQxeC5Aq6BPw.png) 

<figcaption>方框图显示数据中的异常值</figcaption>

令人振奋的是，如果不考虑异常值，我的大部分数据都落在 12500 到 9000 个区域之间。

[![](img/a0830304502f0c4c7b9b55454cd35f87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xZPS4u1F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/569/1%2AduqiuN0-qZkPbgMDXa_81w.png) 

<figcaption>直方图带中值</figcaption>

此外，我想看看我在任何一天能走超过 10000 步的概率。

[![](img/9356a101e0efa907a22c4e5e1af81735.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m9dgWYq4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/544/1%2AEJ5b2bdwmkQa-9bQfzy1lA.png) 

<figcaption>我在任何一天超过 10000 的概率</figcaption>

我将曲线标准化，使用 z 表发现我有 59%的机会在任何一天走超过 10000 步。虽然我会采取这一点，我打算得到更高的比例在 82%左右。

最后，我想看看这个模式，看看是否有连续跑超过 10000 步的记录。

[![](img/28cb5cf984380f4611eeb4ecd04c364c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9w3WEIpX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/609/1%2AFNA_tXGJECiTpLufNEynKg.png) 

<figcaption>热图显示条纹的步数> 10000 或< 10000</figcaption>

上面用红色标出的是我成功完成超过 10000 步或不到 10000 步的日子。我最长的超过 10000 步的纪录是 15 天，我在 2019 年成功了两次。一次从 1 月 9 日到 1 月 23 日。我的连胜在 24 日被打破，然而，我设法回到正轨，并从 2 月 25 日至 8 日开始另一个 15 天的连胜。我一天少于 10000 步的记录也来了两次，连续 8 天了。第一次发生在 11 月 2 日至 9 日，第二次发生在 3 月 16 日至 23 日。

总的来说，看着这些数据，我觉得我没有想象中做得那么好。我预计至少 80%的情况下我会完成 10000 步以上，然而，数据显示并非如此。好的一面是，有了这些数据，我可以展望未来，让我的概率比例接近 80%。