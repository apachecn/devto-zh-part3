# 机器学习工具箱评论:Ax

> 原文：<https://dev.to/banjtheman/machine-learning-toolbox-review-ax-42fi>

机器学习工具箱审查的目标是根据 3 个关键因素确定加速机器学习过程的工具。

1.  入职体验
2.  增值
3.  社区支持

每集将对该工具的表现进行评级，并为该工具提供一个总体评分。

## 什么是斧子？

Ax 是一个可访问的通用平台，用于理解、管理、部署和自动化适应性实验。

基本上给定一组所有可能的解决方案，找到你要解决的问题的最佳解决方案。

Ax 由脸书开源，并于 5 月 1 日在他们的 F8 会议上发布。

## 入职体验

入职体验评估开发人员从产品页面开始使用产品的无缝程度。

### 下载

下载非常简单，使用 pip3(抱歉 python 2.7 的人，但是是时候继续了)

```
pip3 install ax-platform 
```

在漫长的安装之后，给出了示例代码来确保它能够工作

```
from ax import optimize
best_parameters, best_values, experiment, model = optimize(
        parameters=[
          {
            "name": "x1",
            "type": "range",
            "bounds": [-10.0, 10.0],
          },
          {
            "name": "x2",
            "type": "range",
            "bounds": [-10.0, 10.0],
          },
        ],
        # Booth function
        evaluation_function=lambda p: (p["x1"] + 2*p["x2"] - 7)**2 + (2*p["x1"] + p["x2"] - 5)**2,
        minimize=True,
    ) 
```

此示例代码希望找到满足等式的最小数字。实验立即执行，这需要几秒钟的时间。然后将结果存储在 best _ paramters 变量中。

```
{'x1': 0.7810791992092341, 'x2': 3.0911807098099935} 
```

使用起来非常简单，网站上提供了教程来探索更多的用例。[https://ax.dev/tutorials/](https://ax.dev/tutorials/)

安装过程中没有任何停顿，有入门教程和大量文档，这是一次很棒的入门体验，**我给它 5/5** 。

## 值相加

我们可以定义增值，因为使用这个工具可以节省我多少时间。它可以用更少的代码行做更多的事情，或者摆脱手动过程。

Ax 附带了许多预构建的高级 API，封装了大量密集的机器学习代码，只需一个简单的调用。例如，为了进行超参数训练，我们将使用

```
 def train_evaluate(parameterization):
    net = train(train_loader=train_loader, parameters=parameterization, dtype=dtype, device=device)
    return evaluate(
        net=net,
        data_loader=valid_loader,
        dtype=dtype,
        device=device,
    )

best_parameters, values, experiment, model = optimize(
    parameters=[
        {"name": "lr", "type": "range", "bounds": [1e-6, 0.4], "log_scale": True},
        {"name": "momentum", "type": "range", "bounds": [0.0, 1.0]},
    ],
    evaluation_function=train_evaluate,
    objective_name='accuracy',
) 
```

经典方法让你不得不以非常冗长的方式定义每一步。

Ax 还附带笔记本绘图工具，使其与 Jupyter 笔记本无缝配合。

大量的选择，以及进行适应性实验的封装，给这个领域的任何人带来了巨大的附加值，**使它成为一个明确的 5/5** 。

## 社区支持

社区支持可以定义为围绕帮助彼此使用该工具解决问题的社区。这可以通过 Stackoverflow、GitHub 或任何其他类型的社交渠道来实现。

好吧，因为 Ax 是新的，它的背后不是没有喋喋不休。Stackoverflow 上什么都没有，Github 页面上只有几个问题。该工具最近发布后，贡献者反应积极，但由于没有围绕该工具建立太多的社区，我只能建议使用，如果你是该领域有经验的开发人员，在没有支持的情况下也能完成很多工作。**我的 3/5 年级**

## 整体

凭借出色的入职体验和强大的 API 所展示的巨大附加值，该工具将成为适应性实验的主要参与者。缺乏对 Ax 的支持，新手很难打入。希望围绕 Ax 开发一个社区，为它铺平道路，使它更容易接近。**最终评级 4/5**