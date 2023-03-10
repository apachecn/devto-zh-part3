# 多模态学习模式介绍

> 原文：<https://dev.to/kayis/introduction-to-multimodal-learning-model-4ngm>

*在这篇文章中了解 multimodal 是如何工作的，作者是精通构建机器学习平台和应用的 Amir Ziai 以及 BodiData 的计算机视觉和机器学习工程师全华，bodi data 是一个用于人体测量的数据平台。*

* * *

多模态学习不是火箭科学。让我从一个有趣的例子开始。假设你正在镇上参加一个摄影展，有几张来自世界各地的建筑、野生动物等的照片。你的朋友让你简要描述其中一张野生动物照片，而你对照片中的动物一无所知，所以你甚至无法开始描述这张照片。好尴尬！如果我给你一个算法，可以读取你的输入图像，并自动为你生成图像描述，会有帮助吗？计算机算法有没有可能告诉你照片里发生了什么？答案是肯定的:有可能。在本文中，我们将了解多模态学习是如何工作的。

首先，我们将创建一个玩具代码，看看如何使用来自多个来源的信息来开发多模态学习模型。让我们打开 Python 环境，创建一个名为`multimodal_toy.py`的 Python 文件。

我们将需要以下内容:

*   至少两个信息源
*   每个来源的信息处理模型
*   学习组合信息的学习模型

第二点中列出的信息处理模型实际上是一个特征提取器网络。它将负责从输入数据中提取有意义的特征。例如，如果输入是图像，特征提取器将提取关于图像中的图案或对象的特征。

下图显示了多模态信息分析的典型架构:

[![Multimodal Diagram](img/e510209d43741f1ed9993a207c075491.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PZYdGw4Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5kguj4j3r41v2xo0nath.jpg)

正如您在前面的架构中看到的，我们使用了两个不同的特征提取器，因为两个源的信息之间没有相关性。在大多数情况下，这两个来源将来自不同的领域，即视觉和文本。

## 信息来源

现在，让我们试着把这些块放到我们的 Python 文件中。为此，我们将生成一个玩具数据集，它将具有两个高斯分布形式的两个信息源，每个信息源具有不同的均值和方差。先说信息生成。我们将考虑一个发行版作为视觉源，另一个作为文本源。代码清单如下:

```
# Number of Sample points 
n = 400

# Probability of distribution 
p_c = 0.5

# Let's generate a binomial distribution for class variable
# It will divide the 50% samples in both class 1 and class 0 
c = np.random.binomial(n=1, p=p_c, size=n)

# Our two information sources will be 2 bivariate Gaussian distributions
# So we need to define 2 mean values for each distribution
# Mean values for Visual dataset 
mu_v_0 = 1.0
mu_v_1 = 8.0

# Mean values for the textual dataset 
mu_t_0 = 13.0
mu_t_1 = 19.0

# Now we will generate the two distributions here 
x_v = np.random.randn(n) + np.where(c == 0, mu_v_0, mu_v_1)
x_t = np.random.randn(n) + np.where(c == 0, mu_t_0, mu_t_1)

# Let's normalize data with the mean value 
x_v = x_v - x_v.mean()
x_t = x_t - x_t.mean()

# Visualize the two classes with the combined information 
plt.scatter(x_v, x_t, c=np.where(c == 0, 'blue', 'red'))
plt.xlabel('visual modality')
plt.ylabel('textual modality')
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

当我们执行上述脚本时，输出将如下所示:

[![Multimodal Output](img/bce22546fcc47345fcb3741a6b9fc99f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--viZtIYwj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sih6g8g14b1shvh1h5nm.jpg)

正如您在上图中看到的，我们有两个用两种颜色显示的类响应。这两个类都是通过组合两种模态创建的:在 x 轴上，您可以看到视觉模态，而在 y 轴上，呈现的是文本模态。当我们从前面的图中选择任何一点时，它的类别由来自两种模态的组合信息来估计。

我们现在可以从前面的分布中抽取样本。对于推理任务，我们将从前面的分布中创建信息数据。这是按如下方式完成的:

```
# Define the number of points in the sample set 
resolution = 1000

# Create a linear sample set from the visual information distribution 
vs = np.linspace(x_v.min(), x_v.max(), resolution)

# Create linear sample set from the textual information distribution 
ts = np.linspace(x_t.min(), x_t.max(), resolution)

# In the following lines we will propagate these sample points to create
# a proper dataset, it will help to create a pair 
(vs, ts) = np.meshgrid(vs, ts)

# Here we will flatten our arrays 
vs = np.ravel(vs)
ts = np.ravel(ts) 
```

Enter fullscreen mode Exit fullscreen mode

前面的脚本将给出一个一维数组，其中包含从两个不同来源(高斯分布)生成的信息。一旦我们有了样本，我们就可以为两个信息源创建特征提取器了。

## 特征提取器

我们现在将继续设计用于处理这些信息的特征提取器。我们将为此任务创建两个神经网络。一个处理视觉信息，另一个处理文本信息。下面的代码片段将首先提取信息。我们会对信息进行组合，然后传递给最终的分类器，根据训练数据对组合后的信息进行分类。下面的代码片段将创建有用的变量，这些变量可以在以后的架构中使用:

```
# Let's start with creating variables
# It will store the visual information 
visual = tf.placeholder(tf.float32, shape=[None])

# This will store textual information 
textual = tf.placeholder(tf.float32, shape=[None])

# And the final one will be responsible for holding class variable 
target = tf.placeholder(tf.int32, shape=[None])

# As we are working with a binary problem 
NUM_CLASSES = 2

# We will use a fixed number of neurons for every layer 
HIDDEN_LAYER_DIM = 1 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们首先创建了变量来存储来自数据源的数据。我们还创建了一个变量来存储类响应。由于这是一个玩具数据集，我们将通过在每层中使用单个神经元来使我们的网络架构非常简单。在下面的代码片段中，我们将实现用于视觉信息处理的特征提取器:

```
# This is our Visual feature extractor,
# It will be responsible for the extraction of useful features,
# from visual samples, we will use tanh as the activation function. 
h_v = tf.layers.dense(tf.reshape(visual, [-1, 1]), HIDDEN_LAYER_DIM,
                      activation=tf.nn.tanh) 
```

Enter fullscreen mode Exit fullscreen mode

你可以把这个网络想象成一个图像处理网络，用于图像分类或物体识别问题。为了保持网络简单，我们没有使用任何卷积层；我们将使用具有 tanh 激活功能的密集神经元层来代替。

接下来，我们将实现一个网络来处理我们的文本信息:

```
# This is our Textual feature extractor,
# It will be responsible for the extraction of useful features,
# from visual samples, we will use tanh as the activation function. 
h_t = tf.layers.dense(tf.reshape(textual, [-1, 1]), HIDDEN_LAYER_DIM,
                      activation=tf.nn.tanh) 
```

Enter fullscreen mode Exit fullscreen mode

您可以将该网络视为递归神经网络或 LSTM 网络，这是文本预测中使用最广泛的网络之一。这里，我们将使用与视觉特征提取器相似的架构。

## 聚合器网络

现在，我们使用下面的代码片段组合来自两个网络的信息:

```
# Now as we have features from both the sources,
# we will fuse the information from both the sources,
# by creating a stack, this will be our aggregator network 
fuse = tf.layers.dense(tf.stack([h_v, h_t], axis=1), HIDDEN_LAYER_DIM,
                       activation=tf.nn.tanh) 
```

Enter fullscreen mode Exit fullscreen mode

正如您在前面的代码中看到的，我们已经创建了一个已处理信息的堆栈。这就是信息融合发生的地方。这个网络也和我们前两个差不多；唯一不同的是网络的输入，它是以叠加信息的形式存在的。我们现在准备处理这些堆积的信息。以下也是聚合器网络的一部分:

```
# Flatten the data here 
fuse = tf.layers.flatten(fuse)

# Following layers are the part of the same aggregator network 
z = tf.layers.dense(fuse, HIDDEN_LAYER_DIM, activation=tf.nn.sigmoid)

# This one is our final dens layer which used to convert network output
# for the two class 
logits = tf.layers.dense(z, NUM_CLASSES)

# We want probabilities at the output; sigmoid will help us 
prob = tf.nn.sigmoid(logits) 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们刚刚增加了几层。这里没有什么新奇的事情发生。我们使用 sigmoid 概率进行输出，而对于损耗计算，我们使用没有激活函数的简单密集层。

我们应该把所有这些片段一个接一个地放到我们的`multimodal_toy.py`文件中。我们现在需要分析我们网络的性能，以便我们可以相应地训练权重。为此，我们需要定义损失函数。我们将使用 sigmoid 交叉熵来计算网络造成的损失。这可以这样写:

```
# We will use Sigmoid cross-entropy as the loss function 
loss = \
    tf.losses.sigmoid_cross_entropy(multi_class_labels=tf.one_hot(target,
                                    depth=2), logits=logits) 
```

Enter fullscreen mode Exit fullscreen mode

为了优化这种损失，我们将使用具有自适应动量的随机梯度下降或 ADAM 优化器。该优化器将致力于减少网络造成的损失。代码片段如下:

```
# Here we optimize the loss 
optimizer = tf.train.AdamOptimizer(learning_rate=0.1)
train_op = optimizer.minimize(loss) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在准备好训练我们的架构，因为我们已经做好了一切准备。让我们快速写一个函数来照顾我们网络的训练:

```
def train(train_op, loss, sess):

# TRdef train(train_op, loss,sess):
  # TRAIN_OP: optimizer
  # LOSS: calculated loss
  # SESS: Tensorflow session
  # First initialize all the variables created 
    sess.run(tf.global_variables_initializer())

  # We will monitor the loss through each epoch 
    losses = []

  # Let's run the optimization for 100 epochs 
    for epoch in range(100):
        (_, l) = sess.run([train_op, loss], {visual: x_v, textual: x_t,
                          target: c})
        losses.append(l)

  # Here we will plot the training loss 
    plt.plot(losses, label='loss')
    plt.title('loss') 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将创建一个 TensorFlow 会话，并将其与其他参数一起传递给 train 函数。在下面几行中，我们将运行会话，并使用从不同的高斯分布中抽取的样本来训练我们的网络。我们已经提取了这些样本:

```
# Create a tensorflow session 
sess = tf.Session()

# Start training of the network 
train(train_op, loss, sess)

# Run the session 
(zs, probs) = sess.run([z, prob], {visual: vs, textual: ts}) 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行前面的脚本时，我们的模型开始为损失优化进行训练。我们正在创建一个包含每个时期损失值的列表，这将帮助我们监控我们的模型是否正在被训练。

让我们看看我们的损失在培训期间的表现:

[![Training Period](img/4a9933483e805049b0f1be17bb8f9630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2-xjch1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sdfl32x9s64ybgitdnuq.jpg)

在 x 轴上，我们绘制了历元的数量，在 y 轴上，我们绘制了不同的损失值。由于我们已经训练了 100 个纪元的网络，你可以看到有一条非常好的曲线，它显示了我们的模型学习得有多好。损失值的减少表明模型性能的改善是有希望的。

为了在我们的发行版中形象化这一点，我们需要编写一个简单的函数，围绕我们的信息源创建一个决策边界。

让我们看看我们的多模态架构工作得有多好:

```
def plot_evaluations(
    evaluation,
    cmap,
    title,
    labels,
    ):

  # EVALUATION: Probability op from network
  # CMAP: colormap options
  # TITLE: plot title
  # LABELS: Class labels
  # First we will plot our distributions as we have done previously 
    plt.scatter((x_v - x_v.min()) * resolution / (x_v
                - x_v.min()).max(), (x_t - x_t.min()) * resolution
                / (x_t - x_t.min()).max(), c=np.where(c == 0, 'blue',
                'red'))

  # Give the titles to our plots by labeling the axes 
    plt.title(title, fontsize=14)
    plt.xlabel('visual modality')
    plt.ylabel('textual modality')

  # Here we will create a color map to draw the boundaries 
    plt.imshow(evaluation.reshape([resolution, resolution]),
               origin='lower', cmap=cmap, alpha=0.5)

  # Let's put a color bar to create a fancy looking plot 
    cbar = plt.colorbar(ticks=[evaluation.min(), evaluation.max()])
    cbar.ax.set_yticklabels(labels)
    cbar.ax.tick_params(labelsize=13) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在准备通过调用我们的函数来绘制我们的决策边界。这显示在下面的代码中:

```
# We will plot the probabilities 
plot_evaluations(probs[:, 1], cmap='bwr', title='$C$ prediction',
                 labels=['$C=0$', '$C=1$'])

# Show the plots over here 
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

以下是前面几行代码的执行结果，向我们展示了决策边界:

[![Decision Boundary](img/946cead971759c141bfc12ebeeb2e7d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jAMqVPap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/68cyw185njzbceynsj8q.png)

这正是我们希望从我们的网络中得到的。如您所见，我们已经处理了从两个不同来源收集的信息，并将样本正确分类。这就是多模态学习的工作方式:我们收集信息，并将其结合起来，以获得显著的效果。

* * *

*如果你觉得这篇文章很有趣，你可以通过 TensorFlow 探索[动手人工智能，了解机器学习和深度学习中用于构建智能应用的有用技术。](https://www.amazon.com/Hands-Artificial-Intelligence-TensorFlow-applications/dp/1788998073)*