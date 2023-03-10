# 路德维希的无代码机器学习

> 原文：<https://dev.to/chrishunt/code-free-machine-learning-with-ludwig-2gap>

## Intro 和 Ludwig

2019 年 2 月初，优步将他们的无代码机器学习工具箱 Ludwig 开源。

网站-[https://uber.github.io/ludwig/](https://uber.github.io/ludwig/)
用户指南-[https://uber.github.io/ludwig/user_guide/](https://uber.github.io/ludwig/user_guide/)T5】Github 回购-[https://github.com/uber/ludwig/](https://github.com/uber/ludwig/)

Ludwig 运行在流行而强大的 TensorFlow 库之上，并提供 CLI 访问，以实验和训练机器学习模型，并使用 TensorFlow 模型进行预测

作为一名工程师，我绝对不是数据科学家。我对 TensorFlow 有足够的了解，可以使用教程建立最基本的模型，但真的不能从头开始创建任何东西。路德维希提供了这个机会。

## 我们的第一个实验

让我们深入研究一个基本的例子。我们将尝试用零行代码在[https://www.tensorflow.org/tutorials/keras/basic_regression](https://www.tensorflow.org/tutorials/keras/basic_regression)重新创建 Keras 教程。

该数据集显示了[自动 MPG 数据集](https://archive.ics.uci.edu/ml/datasets/auto+mpg)中汽车的基本数据。我们的任务是根据提供的特征预测 MPG。我将它转换成一个 CSV 文件，供本例使用。

Ludwig 使用模型定义文件来确定构建模型的参数。Ludwig 的内部处理你的数据。它创建训练、测试和验证数据集。它还根据您指定的数据类型，将数据修改为最佳的训练格式。

Keras 示例需要我们操作数据，以便训练和测试模型。路德维希为我们做了这一切。它允许我们通过在`modeldef.yaml`建立模型定义文件来立即训练模型。在这里，我们定义输入要素及其数据类型。对于更复杂的模型，可以针对每个特征设置许多其他参数。我们还定义了输出特征及其参数。

```
input_features:
  - 
    name: Cylinders
    type: numerical
  - 
    name: Displacement
    type: numerical
  - 
    name: Horsepower
    type: numerical
  - 
    name: Weight
    type: numerical
  - 
    name: Acceleration
    type: numerical
  - 
    name: ModelYear
    type: numerical
  - 
    name: Origin
    type: category
output_features:
  - 
    name: MPG
    type: numerical 
```

### 首次运行

我们的第一个实验现在可以用以下命令运行:

```
ludwig experiment  --data_csv cars.csv --model_definition_file modeldef.yaml --output_directory results 
```

这给出了以下结果:

```
===== MPG =====
loss: 52.9658573971519
mean_absolute_error: 6.3724554520619066
mean_squared_error: 52.9658573971519
r2: 9.58827477467211e-05 
```

完成 200 个周期后，我的平均绝对误差(MAE)为 6.4(根据随机训练/测试划分，您的误差可能略有不同)。这意味着平均每加仑汽油的预测值与实际值相差 6.4 英里。请记住，数值通常在 10MPG 和 47MPG 之间，6.4MPG 代表相当大的误差。

### 细化

如果您在 Ludwig 运行时查看日志滚动，您会看到验证集的 MAE 随着每个时期而减少。

Keras 示例建议最终 MAE 为 2，因此我们可能需要做一些调整才能更接近。有明显的迹象表明，随着挤兑的结束，货币平均汇率仍在下降。我们可以通过简单地增加模型定义来增加历元的数量

```
training:
  epochs: 400 
```

并使用命令继续前面的训练模型

```
ludwig experiment  --data_csv cars.csv --model_definition_file modeldef.yaml --output_directory results -mrp ./results/experiment_run_0 
```

我们的 MAE 只下降到 5.3MPG。仍然没有那么接近。

### 进一步细化

在现实生活的例子中，我们开始修改超参数，再训练，修改，再训练，而我们的目标 MAE 仍然降低。

我们将跳过这一步，复制 Keras 教程中的超参数:

```
training:
  batch_size: 32
  epochs: 400
  early_stop: 50
  learning_rate: 0.001
  optimizer:
    type: rmsprop 
```

此外，我们将早期停止设置在 50 个时期——这意味着如果我们的验证曲线在 50 个时期内没有改善，我们的模型将停止训练。实验以和以前一样的方式开始。它会产生以下结果:

```
Last improvement of loss on combined happened 50 epochs ago

EARLY STOPPING due to lack of validation improvement, it has been 50 epochs since last validation accuracy improvement

Best validation model epoch: 67

loss: 10.848812248133406
mean_absolute_error: 2.3642308198952975
mean_squared_error: 10.848812248133406
r2: 0.026479910446118703 
```

我们得到一条消息，我们的模型已经在 132 个时期停止训练，因为它达到了早期停止限制。

MAE 下降到 2.36MPG，而没有编写一行代码，我们已经得到了与 Keras 教程类似的结果。

### 观想我们的训练

现在我们要验证我们的测试和验证损失曲线非常接近，但没有显示过度拟合。Ludwig 继续兑现其无代码解决方案的承诺。我们可以使用以下命令查看我们的学习曲线:

```
ludwig visualize -v learning_curves -ts results/experiment_run_0/training_statistics.json 
```

[![Learning curves](img/9c801e04975e26b29823f17424fe0c19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcNK2oTD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mfpzv0dwf0xt77pxkjns.png)

曲线仍然遵循相似的轨迹。如果验证曲线开始向上，而训练曲线保持在该轨迹上，则表明过度拟合正在发生。

## 现实生活验证

好吧，这一切都很好，但教程出了名的挑选数据，所以输出“只是工作”。让我们用一些真实数据来检验我们的模型。

经过一番调查，我找到了 DeLorean DMC-12 所需的统计数据([https://en . Wikipedia . org/wiki/DMC _ delore an):](https://en.wikipedia.org/wiki/DMC_DeLorean):)

```
Cylinders:     6
Displacement:  2849cc (174 cubic inches)
Horsepower:    130hp
Weight:        1230 kg (2712 lb)
Acceleration:  10.5s
Year:          1981
Origin:        US 
```

并将其转换为与训练数据相同的 CSV 格式:

```
Cylinders,Displacement,Horsepower,Weight,Acceleration,ModelYear,Origin
6,174,130,2712,10.5,81,1 
```

现在，为了预测它的燃油经济性，我们通过 Ludwig 运行`predict`命令:

```
ludwig predict --data_csv delorean.csv -m results/experiment_run_0/model -op 
```

我们指定了`-op`标志来告诉 Ludwig 我们只需要预测。输入一个包含 MPG 列的 CSV 文件，并且不添加此标志，将会运行预测，而且还会为我们提供文件中提供的实际值的统计数据。

我的模型给出的结果是 23.53405mpg，这有多好？不幸的是，我们的维基百科文章没有显示已公布的燃油经济性，但我确实设法在这篇关于这款神奇汽车的精彩文章中找到了它——22.8 英里/加仑。一个相当不错的现实生活测试！

## 总结

我很感激数据科学家们大声疾呼，说我们没有对输入特征进行任何分析来创建有意义的特征集，也没有对测试数据预测进行具体分析。我也意识到 MAE 不一定是准确性的最终度量，因为它可能会被离群值严重扭曲，我们可以通过进一步的分析来验证这一点。

我们展示的是，使用 Ludwig，我们可以实验和训练一个机器学习模型，然后使用我们训练的模型进行预测。

机器学习变得越来越容易获得。路德维希在这方面似乎前进了一大步。