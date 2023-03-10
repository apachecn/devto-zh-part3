# 匕首解释道

> 原文：<https://dev.to/sdan/dagger-explained-2np9>

* * *

# 注意:LaTex 不*不*在 dev.to 上正确渲染。你可以在这里找到这篇帖子的 LaTex 丰富版本:【https://blog.suryad.com/dagger/】T4

### 注:本文所有素材改编自 Sergey Levine 的 CS294-112 2017/2018 课

[![DAgger Explained](img/5d0ee48fd11bb13768b253268e3b121a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EzXV0t-j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.suryad.com/conteimg/2019/08/dagger-face-1.png)

数据集聚合，通常被称为 DAgger，是一种相对简单的迭代算法，它训练一种完全依赖于原始和生成的数据集的状态分布的深度确定性策略。[1]

## 这里有一个简单的例子:

假设你在教别人开车。在这种情况下，你是一个人([希望是](https://www.youtube.com/watch?v=pYslwSw8IMo))，你朋友的名字是戴夫。尽管同为人类，戴夫无法驾驶，这使他像一个政策(\pi_{\theta}(a_t \vert o_t))。其中(\pi_{\theta})是参数化策略，而(a_t)是来自(o_t)的采样动作空间，o _ t 是观察空间。

但是 Dave 很聪明，他事先通过观看一些 YouTube 视频为这个练习进行了训练，我们可以将其表示为(p_{data}(o_t))。像我们其他人一样，戴夫并不完美，他也会犯错。然而，在这种情况下，戴夫的错误会很快累积起来，导致他(以及你)最终掉进沟里或撞到别人。我们可以将观察到的戴夫分布表示为(p_{\pi_{\theta}}(o_t))。随着戴夫开车，他的错误越来越多，最终导致他偏离了(p_{data}(o_t))，这是戴夫训练的数据(那些 YouTube 视频)。

<figure>[![DAgger Explained](img/9b75642bb5100f74e3c70355776a708e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---cshuO-u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://data.suryad.com/assets/blog/dagger_schulman.png) 

<figcaption>摘自约翰·舒尔曼《柏克莱深 RL 2015》。链接:[http://RLL . Berkeley . edu/deeprlcourse-fa15/docs/2015 . 10 . 5 . dagger . pdf](http://rll.berkeley.edu/deeprlcourse-fa15/docs/2015.10.5.dagger.pdf)【3】</figcaption>

</figure>

把戴夫想象成图中的红色轨迹。你知道蓝色轨迹在你的脑海中是正确的，随着时间的推移，你试图告诉戴夫像蓝色政策那样驾驶。

那么我们该如何帮助可怜的戴夫呢？戴夫最初看了 YouTube 上的视频，想了解一下如何驾驶，之后他记录下了自己的驾驶过程。在驾驶的每一集之后，我们汇总这些数据，并添加 Dave 应该采取的行动，以获得新的数据集(\mathcal{D_{\pi} ),该数据集不仅包括({o_1，\ldots，o_M})，还包括与这些观察相对应的行动(因为我们填写了他应该对这些观察采取的行动):({o_1，a_1，\ldots，a_M，o_M})

<figure>[![DAgger Explained](img/c731be4c9328cb48d0cde81529fbbb95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v7U5rhAH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://data.suryad.com/assets/blog/dagger_trajectory.png) 

<figcaption>戴夫的轨迹是红色的，而你知道黑色的轨迹是最优的。Dave 根据红色轨迹创建了一个数据集，您的任务是填写 Dave 所做的每个观察的行动。摘自 2017 年 8 月 2 8 日由 Sergey Levine 撰写的 CS294-112。</figcaption>

</figure>

【4】

现在，我们将旧数据集，(\mathcal{D})与这个“新”数据集，(\mathcal{D_{\pi}})聚合在一起，如下所示:(\ mathcal { D } \ left arrow \ mathcal { D } \ cup \ mathcal { D _ { \ pi })

当然，你可以用任何你想要的方式聚集数据，但是这是一个简单的方法。

* * *

在那个例子中，我们实际上在 Dave 身上实现了 DAgger！随着时间的推移，戴夫的分布不匹配将不复存在，这意味着戴夫将成为一个驾驶传奇。

让我们把上面的例子转化成一个算法:

<figure>[![DAgger Explained](img/4c59b34121197e7ac11004a564fd36cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1iFBo_jU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://data.suryad.com/assets/blog/dagger_algorithm.png) 

<figcaption>由 Surya Dantuluri 渲染，由 Ross，Gordon，Bagnell，Levine [1]，</figcaption>

</figure>

改编

让我们一步一步地研究这个算法:

1.  我们用初始数据集初始化数据集(\mathcal{D})。
2.  初始化某个策略(\pi_{\theta}(a_t \vert o_t))
3.  对于 N 步(这取决于你要迭代这个算法多少次。这个算法迭代的次数越多，(p_{\pi_{\theta}}(o_t))就越像(p_{data}(o_t))
4.  现在在 for 循环中，我们从 policy(\ pi _ { \ theta }(a _ t \ vert o _ t))采样一个轨迹
5.  我们从基于专家数据集的(p_{data}(o_t))得到观察值的分布
6.  一旦我们有了观察值的分布，我们就可以加入策略(\pi_{\theta}(a_t \vert o_t))应该采取的行动
7.  然后，我们将刚刚创建的新数据集与初始数据集进行聚合
8.  在这个大数据集(\mathcal{D})上训练分类器(\pi_{\theta}(a_t \vert o_t))
9.  只要你愿意，重复这个循环，因为(\pi_{\theta}(a_t \vert o_t))随着时间的推移变得更好，并且渐近地它的(p_{\pi_{\theta}}(o_t))将像(p_{data}(o_t))

* * *

## 实现

那么如何在实践中实现 DAgger 呢？

这个算法很简单，所以我们只需要把伪代码翻译成 Python，现在我们不讨论 setup/等。

这个实现不需要人工。由于 DAgger 是一个行为克隆(BC)算法，我们可以对一个专家运行 DAgger 来最终克隆这个专家的性能，而不是克隆一个人的行为。

这个实现是基于[https://github.com/jj-zhu/jadagger](https://github.com/jj-zhu/jadagger)的。[5]

用 tf.train.MonitoredSession()作为 sess:
sess . run(TF . global _ variables _ initializer())
# record return 和 STD for plotting
save _ mean =[]
save _ STD =[]
save _ train _ size =[]
# loop for dagger alg

这是我们要开始的张量流会话的初始化。注意，我换的是普通 tf。Session()到 tf.train.MonitoredSession()，因为它有一些好处。除此之外，我们初始化一些我们将在算法中使用的数组。

# loop for DAgger alg
for I _ DAgger in xrange(50):
print ' DAgger iteration '，i_dagger
# train a policy 拟合 MLP
batch _ size = 25
for step in range(10000):
batch _ I = NP . random . randint(0，obs_data.shape[0]，size = batch _ size)
train _ step . run(feed _ dict = { x:OBS _ data[batch _ I，

在这里，我们将 DAgger 算法设置为迭代 50 次。在 for 循环中，我们首先训练我们的策略(\pi_{\theta}(a_t \vert o_t))以适应 MLP，它代表多层感知器。它本质上是一个标准的前馈神经网络，具有输入、隐藏和输出层(非常简单)。

换句话说，鉴于这是第一次迭代，前面例子中的 Dave 刚刚通过观看 YouTube 视频获得了一些关于如何驾驶的想法。在后面的迭代中，Dave，(\pi_{\theta}(a_t \vert o_t))本质上是在整个数据集上进行训练(\mathcal{D})。这是无序的，但这是我们算法中的第 6 步。不过这并不重要，因为算法的所有部分都包含在这个实现中，按顺序是**。**

# 用受过训练的 MLP 来表演

```
 max_steps = env.spec.timestep_limit

        returns = []
        observations = []
        actions = []
        for i in range(num_rollouts):
            print('iter', i)
            obs = env.reset()
            done = False
            totalr = 0.
            steps = 0
            while not done:
                action = yhat.eval(feed_dict={x:obs[None, :]})
                observations.append(obs)
                actions.append(action)
                obs, r, done, _ = env.step(action)
                totalr += r
                steps += 1   
                if render:
                    env.render()
                if steps % 100 == 0: print("%i/%i" % (steps, max_steps))
                if steps &gt;= max_steps:
                    break
            returns.append(totalr)
        print('mean return', np.mean(returns))
        print('std of return', np.std(returns)) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们推出了我们的策略 Dave(换句话说(\pi_{\theta}(a_t \vert o_t))。策略推出是什么意思？

<figure>[![DAgger Explained](img/b66dfdd84c864671f546bd274c11f077.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s-0wuZBK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://data.suryad.com/assets/blog/dagger_rolly.jpg)

<figcaption>[https://www.uihere.com/free-cliparts/search?q=Roly-poly&amp；page = 2](https://www.uihere.com/free-cliparts/search?q=Roly-poly&page=2)</figcaption>

T8】</figure>

这和一个不倒翁聚推出来有关系吗？正如你所猜测的，事实并非如此。相反，它本质上是我们的策略探索轨迹，最终建立(p_{data}(o_t))的分布。这是我们算法中的第二步和第三步。

#专家标注
act _ new =[]
for I _ label in xrange(len(observations)):
act _ new . append(policy _ fn(observations[I _ label][None，:)
#记录训练大小
train _ size = OBS _ data . shape[0]
#数据聚合
OBS _ data = NP . concatenate((OBS _ data，np.array(observations))，axis=0)
act_data = np。

```
dagger_results = {'means': save_mean, 'stds': save_std, 'train_size': save_train_size,
                  'expert_mean':save_expert_mean, 'expert_std':save_expert_std}
print 'DAgger iterations finished!' 
```

Enter fullscreen mode Exit fullscreen mode

我们这里有匕首所必需的最后一道工序。在这里，我们用我们的专家策略专业地标记(p_{data}(o_t))的分布。这是我们算法中的第 4 步和第 5 步。

那不是很有趣吗？希望你对匕首有更好的理解。你可以在贾的[回购](https://github.com/jj-zhu/jadagger)上找到这个代码。如果您有任何问题，请随时拨打[【电子邮件保护】](https://blog.suryad.com/cdn-cgi/l/email-protection)联系我

* * *

**参考文献**

[1]将模仿学习和结构化预测简化为无悔在线学习，Ross，Gordon & Bagnell (2010)。匕首算法[ [链接](https://arxiv.org/abs/1011.0686)

[2]室内场景导航的深度匕首模仿学习[ [PDF](http://cs231n.stanford.edu/reports/2017/pdfs/614.pdf)

[3]匕首和朋友[ [PDF](http://rll.berkeley.edu/deeprlcourse-fa15/docs/2015.10.5.dagger.pdf)

j .舒尔曼，2015。

[4] CS294-112 Fa18 8/24/18 [ [链接](https://www.youtube.com/watch?v=yPMkX_6-ESE&index=24&list=PLkFD6_40KJIxJMR-j5A1mkxK26gh_qg37)

莱文，s，2018。

[5] jadagger [ [链接](https://github.com/jj-zhu/jadagger)

朱，J.J，2016。