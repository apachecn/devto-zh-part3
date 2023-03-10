# 在代码峰值后向前移动

> 原文：<https://dev.to/joecannatti/moving-forward-after-a-code-spike-2bd5>

编写代码通常是找出解决问题到底需要做什么的最佳方式。在敏捷世界中，他们称之为[峰值](http://agiledictionary.com/209/spike/)。

尖峰是指软件开发人员坐下来，开始写一点点代码，目标是建立一个他们最终需要完成项目的所有工作的地图。

这是一种快速编写代码的方式，它可以做如下事情:

*   查询数据库以查看其中有什么以及它们是如何组合在一起的
*   调用第三方 API 来了解它们是如何工作的
*   测试 Fred 说会在空闲时工作的算法，看看他是否正确
*   在提交设计之前可能需要验证的任何其他内容。

峰值通常会导致一堆混乱的代码，这些代码差不多完成了产品最终需要做的大部分事情。对于大多数程序员来说，做一个 spike 感觉很好。与其他编写代码的方法相比，它能给人更多的即时满足感。这让我觉得自己像一股不可阻挡的自然力量，穿越时空，完全意识不到自己的巨大平庸。正因为如此，当峰值完成时，有一种感觉大部分工作都完成了的诱惑。

接下来，开发人员通常会说类似这样的话，“我只需要从峰值中清除代码，并打开一个拉请求”。这给人留下了没有多少工作要做的印象。坏消息是，这种印象往往是错误的。好消息是，我现在要告诉你如何处理这个坏消息。

## 峰值代码和生产代码有什么明显的不同？

### 一切缘例。我是说所有人。

执行峰值测试和编写产品代码之间的一个主要区别是，当边缘情况变得清晰时，我们会做什么。

在生产代码中，我会立即编写一个测试和/或修改代码来覆盖这种场景。在尖峰，我只是不停地滚动。最多，我会给自己记下这件事。

这些随着时间的推移而积累。花三天时间编写 spike 代码可能会在代码中留下比您想象的更多的未处理案例。

### 长期可维护性

在高峰期编写的代码通常很少考虑可维护性。

这是我最近做的一个 spike 中的一个例子，它涉及到从一个内部 API 中提取关于学生群体的所有数据。

```
def cohort_ids 
  open = cohorts_api_client.search_cohorts(status: 'open', limit: 5000) 
  active = cohorts_api_client.search_cohorts(status: 'active', limit: 5000) 
  upcoming = cohorts_api_client.search_cohorts(status: 'upcoming', limit: 5000) 
  (open + active + upcoming).map(&:cohort_id).map(&:to_i).sort 
end 
```

当时我们的队列远远少于 5000 人。因此，为了我的尖峰，设置限制为 5000，打一个电话是好的。但是，如果我们有超过 5000 个特定身份的群体，会发生什么呢？该代码会错过它们。正确的做法是一页一页地翻阅结果，直到没有结果为止。

实际投入生产的代码看起来是这样的

```
BATCH\_SIZES = 100 

def all 
  %w(open active upcoming).map do |status| 
    fetch(status: status)
  end.inject(&:+).uniq 
end 

protected 

def fetch(status:, limit: BATCH\_SIZES, offset: 0) 
  batch = cohorts_api_client.search_cohorts(status: status, limit: limit, offset: offset) 
  if batch.count == limit
    batch + fetch(status: status, limit: limit, offset: offset + limit) 
  elsif batch.count < limit 
    batch 
  end
end 
```

这是一个非常重要的重构。这也正是当编码人员急于将尖峰代码投入生产时容易被忽略的事情。现在，spike 版本的代码会工作得很好。但是在未来的某一天(可能是最糟糕的时刻)，将会有一大批新的群体涌入，原始代码将会彻底崩溃。这将粉碎编码者对他们正在做的事情有任何想法的幻想，不仅仅是在他们的工作中，而是在他们的生活中。所以让我们避免这种情况。

### 可测性

尖峰代码通常不是由编写测试驱动的，或者用来驱动它的测试类型不是产生良好设计的最佳类型。

对于上面列出的峰值代码，我用一个高级集成测试来驱动开发。这个测试端到端地运行了峰值中的所有代码。用单元测试来驱动代码，单元测试孤立地执行一小部分代码，通常会产生更好的设计。这最终成为 spike 代码无法投入生产的主要原因。

## 公关会是什么样子？

另一件需要考虑的事情是拉取请求应该是什么样子。在大多数情况下，如果没有一些刻意的努力，它会变得太大。

我喜欢在 Github 上将分支与 master(或 develop)进行比较，这样我就能确切地看到它会是什么样子。这是理解 spike 代码实际上还没有准备好投入生产的一个很好的起点。

## 我们如何走出这个烂摊子？

#### 1。获取相关的代码，并将其放入某种 notes 文档中。

我就不多说了，我多么喜欢 emacs/org-mode 做这种事情…

#### 2。看着每一个，打出来它是做什么的，它是怎么做的，以及(最重要的)它有什么不完美的地方。

你可能认为你已经知道了这些事情，可以跳过这一步，但是不要！

#### 3。创建一个不包含你的 spike 代码的 master(或 developer 或其他)的新分支。

#### 4。决定构成完整拉取请求的最小功能块

在我最近的一篇文章中，我的目标是将它转化为拉请求，基本上可以做到以下几点

1.  设置用于访问相关 API 的凭证
2.  从 API 中提取数据
3.  设置 OO 设计来对数据进行计算
4.  每个计算的字段一个 PR

可能会有 5 或 6 个独立的 PRs。

#### 5。构建该功能。当你需要的时候，从你的钉上取下笔记中的代码片段。

#### 6。对您复制的每个代码片段进行特别检查，以确保单元测试完全覆盖了代码中的所有路径，并且您之前写的任何不足之处都得到解决。

这是最重要的一步！

#### 7。打开 PR，获得反馈，进行更改，合并。

#### 8。回到第三步，重复直到所有的工作都完成

## 这是一门艺术…

做到这一点并不总是容易的。例如，有时很难将你的尖峰信号分解成一堆小的信号。随着时间的推移，一个人会对什么是最好的结果产生部分直觉的偏好。培养这种品味的唯一方法是，每次你发现自己处于这种情况时都尽最大努力。

祝你好运！