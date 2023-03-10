# 做数据科学和深度学习的一年

> 原文：<https://dev.to/rohitfarmer/one-year-of-doing-data-science-amp-deep-learning-39ip>

在过去的一年里，我一直在做数据科学(DS)和深度学习(DL)，以了解药物的生物活性、毒性及其对药物性肝损伤(DILI)的影响。我来自分子建模的背景，这是一种分析分子的计算方法，但我不会认为它是 DS 或 DL。由于 DS 和 DL 是我开始掌握的一门新学科，这篇博客是我一年旅程的记录。

在我等待目前职位的签证批准期间，我问我的雇主，我是否应该学习一些东西，以帮助我在深度学习方面有一个良好的开端。答案是运行在 Python 上的 Keras 或 Tensorflow。好了，我学习了基本的 Keras 和 Tensorflow，并建立了 Python 编程语言的中级专业知识。然而，如果有人想学习 DL，如果他只知道普通的 Python 编程，那么 Keras 或 Tensorflow 可能不是第一个开始的地方。DL 项目中的大部分时间花在数据争论上，这意味着以正确的格式将输入数据呈现给 DL 算法，并重新格式化 DL 输出以进行推理。

因此，在我看来，一个很好的起点是学习如何使用数据处理和操作库，比如 Numpy 和 Pandas。这是你在任何 DL 项目中使用的两个最重要的工具。接下来，学习 Matplotlib 将帮助您绘制输入数据和结果的图表。您可以随时使用代码编辑器来练习这些工具，但没有什么比 Jupyter notebook 更好了。在 Jupyter 中，你可以边学边记笔记，在单个单元格中执行小代码片段，还可以在同一个空间中绘制图表。它适合尝试和测试代码，尤其是在数据争论阶段，因为它允许您有效地可视化 Numpy 和 Pandas 数据帧。

一旦你掌握了 Numpy、Pandas 和 Matplotlib，强烈建议你熟悉 Scipy 和 Scikit-Learn。它们是两个通用的数据科学和机器学习库，在 DL 项目中对于各种目的来说非常方便。我比 Keras 或 Tensorflow 更经常使用它们。一些用途是数据缩放、创建训练测试分割、生成交叉验证的分层折叠、ROC-AUC、MSE、MAE 等指标。、特征选择、统计测试等等。

在我看来，除了 Keras 或 Tensorflow 之类的主要 DL 库之外，这些是您促进您的 DL 项目所需的最基本的工具。如果你想在你的帽子上增加一些额外的羽毛，我可以推荐学习 Seaborn 和 Plotly 的图形和情节。它们基于 Matplotlib，但是具有增强的特性。Pyarrow Parquet 是一个库，可以利用 Parquet 格式进行列数据存储。有时，我也使用 Stats 模型库进行一些常规的统计程序。

现在，谈到 Keras 和 Tensorflow，人们通常会从 Keras 开始，因为它很简单，所以它是几个 DL 引擎(包括 Tensorflow)的高级抽象 API。但是，学习 Tensorflow 本身就有很多诱因。Tensorflow 让您可以更好地控制您的 DL 算法。毫无疑问，Keras 是正确的起点，也非常适合于原型开发，并且非常适合于许多可以使用现成的 DL 过程解决的项目。然而，如果你长期使用 DL，那么张量流是不可避免的。

如果你是一个像我一样来自非编程背景的人，那么你也想学习一些额外的工具，这些工具是任何专业程序员工具箱的一部分。可能没有什么比版本控制系统更重要了(VCS)。目前，我猜最广泛使用的两个 VCS 是 Git 和 Mercurial。它们都可以安装在您的本地机器上，也可以在云中使用。Bitbucket 支持 git 和 Mercurial 库，GitHub 支持 Git。拥有一个 GitHub 档案不仅有利于跟踪你的程序，还可以作为一个代码文件夹，你可以在工作面试时展示。在这一点上，我甚至应该提到我最喜欢的代码编辑器，即 Microsoft Visual Studio 代码。它是免费的，跨平台的，官方支持 Python 和 GitHub 集成。

最后一条建议是，DL 只有在你试图学习的学科领域知识的范围内才有用。如果你没有问正确的问题，没有提供正确的数据给 DL，它不会神奇地为你找到答案。

由 unsplash-logo
Fredy Jacob 在 unsplash 上拍摄的顶部横幅照片