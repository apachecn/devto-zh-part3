# 使用 TensorFlow.js 改善用户体验

> 原文：<https://dev.to/djcordhose/improving-user-experience-with-tensorflow-js-4693>

假设您有一个带有一些按钮或其他交互元素的应用程序。为了明确你会点击哪个按钮，当你悬停在它们上面时，它们通常会高亮显示。很像这样

[![Buttons highlighting when hovering over them](img/0031dd61560c5fe9f8d33afde1a9465f.png "Our Application")](https://res.cloudinary.com/practicaldev/image/fetch/s--FVnUmbLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/DJCordhose/ux-by-tfjs/master/img/app.gif)

现在想象一下，在你将鼠标悬停在交互元素上之前，我们能够高亮显示你将要点击的交互元素。这至少有两个原因

1.  提前准备资源
    *   将图像或 iframe 的延迟加载切换到 eager earlier([https://dev . to/Ben/native-lazy-loading-for-img-and-iframe-is-coming-to-the-web-55on](https://dev.to/ben/native-lazy-loading-for-img-and-iframe-is-coming-to-the-web-55on))
    *   无服务器功能预启动
    *   模块预加载
    *   阿瑟:人们很快会看哪里？渲染更多细节
2.  (不明显地)突出显示按钮，使用户更容易访问，从而给他们更好的用户体验，而他们不必知道为什么

这样的高亮显示系统可以是这样的。我们没有把高亮显示做得非常微妙，以便弄清楚发生了什么:

[![Buttons highlighting when the machine thinks I am going to click them soon](img/9999b3a1c75db38e706147f3c0ff33a1.png "Highlighting in Advance")](https://res.cloudinary.com/practicaldev/image/fetch/s--fMyn9vzA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/DJCordhose/ux-by-tfjs/master/img/simpleRNN.gif)

这只是预测按钮的一种方式，但是有很多预测方式会将您正在移动的路径考虑在内，比如这个

[![Buttons highlighting when the machine thinks I am going to click them soon](img/18c2d7c5113f63bc6f7bc8b002f343ad.png "Highlighting using the Path")](https://res.cloudinary.com/practicaldev/image/fetch/s--sSgWBOlw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/DJCordhose/ux-by-tfjs/master/img/ux-predict-3.gif)

这样，该系统将比第一个系统更早地具有可靠的预测，但是代价是不太确定。

## 但是这怎么可能呢？

我猜可能有一种启发式方法，要么在我们靠近一个按钮时高亮显示它，同时比任何其他按钮都近，要么做一个简单的回归，通过你的鼠标移动画一条线，看看你正在指向什么按钮。

不过，这可能有点棘手，因为不同的电脑会有不同的分辨率、不同的触控板或鼠标。更重要的是，不同类型的用户会根据他们的经验和个性使用不同的设备。例如，我喜欢灵敏度极高的触控板，有时会让我跳过一个需要后退一点的按钮。

## 机器学习来救援

机器学习的想法是，我们收集一些关于特定机器上的特定用户如何点击这些按钮的信息。利用这些数据，我们希望创建一个更通用的模型，可以预测用户界面的未来用途。

在我们的例子中，我们可以很容易地收集鼠标事件，这些事件给我们一系列的鼠标位置和与前一个位置的差值。此外，我们可以在事件之间添加一个时间增量。这实际上为我们提供了一个随时间变化的五个数据点序列，您可以在浏览器中的这个开发人员工具屏幕截图中看到:

[![Screenshot from dev tools of mouse sequence data in local storage](img/6055332a4fd90e5ef475c3102d629dd5.png "Mouse Sequence Data")](https://res.cloudinary.com/practicaldev/image/fetch/s--HNrpzFUU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/DJCordhose/ux-by-tfjs/master/img/mouse-positions.png)

我们将这个序列存储在名称为`x`的名称下，以及在鼠标移动序列之后被点击的按钮的代码，如`y`。在这个例子中，`1`可能代表第一个按钮。这意味着我们有许多序列，每一个导致按钮被按下的鼠标移动都有一个序列，在我们的例子中有十个。

## 隐私

在我们谈论如何使用这些数据之前，让我们先谈谈隐私。你希望你的鼠标离开你的电脑的每一个动作都成为你个人行为的模型吗？虽然有些人可能不会介意，但有些人——包括我自己——会介意。我们的解决方案是将数据保存在您的浏览器和磁盘中，并在浏览器中训练模型，同时将其作为默认设置保留在您的浏览器中。所以，无论是数据还是训练好的模型都不需要离开你的浏览器。

## TensorFlow.js

[![TensorFlow.js is a library for developing and training ML models in JavaScript, and deploying in browser or on Node.js](img/6f0eee6cad9c2dd73f0efc50dd10ab1d.png "TensorFlow.js")](https://res.cloudinary.com/practicaldev/image/fetch/s--D6Xf3I_y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/DJCordhose/ux-by-tfjs/master/img/tfjs.png)

使用名为 [TensorFlow.js](https://www.tensorflow.org/js/) 的 Google 著名 TensorFlow 库的 JavaScript 实现完全可以做到这一点。我们需要定义一个与我们的数据格式匹配的神经网络，然后我们训练该网络，最后我们使用该网络来预测下一个被点击的按钮。这一预测将基于新的、以前未知的数据。即使是这些数据也可以用来随时改进我们的模型。所有这些都可以在后台透明地发生。用户可能会注意到的唯一一件事是他们的机器变热或电池电量下降，因为训练是在用户的 GPU 上执行的，这需要相当多的能量。

## 实际模型

我们需要指定一个简单的模型，从而快速训练。它还需要在只有很少训练数据的情况下给出合理的结果。即使是超级用户也可能一分钟内产生不到 100 次点击，所以我们应该能够从这些数据中得到至少一些结果。

能够处理顺序数据的最基本类型的网络层是普通递归神经网络。如上所述，作为输入，它将采用多个鼠标事件(SEGMENT_SIZE ),每个事件有 5 (N_FEATURES)个条目`(posX, posY, deltaX, deltaY, deltaT)`。我们还需要指定我们需要多少个神经元——越多越强大，越需要资源——所以我们指定 50 个，这并不多。

这里，神经网络的*输入*用 TensorFlow.js 表示，如果您熟悉著名的 Keras API，就可以读懂:

```
input = tf.layers.simpleRNN({
    inputShape: [SEGMENT_SIZE, N_FEATURES],
    units: 50
}) 
```

因为我们需要让网络预测用户将点击的按钮之一，所以我们用一个神经元对每个按钮进行编码，该神经元输出按钮的可能性作为神经网络的*输出*:

```
output = tf.layers.dense({
    units: 3,
    activation: "softmax"
}) 
```

在这种情况下，我们有三个按钮，因此有三个神经元。`softmax`指定神经元吐出的概率加起来是 1.0。

我们现在需要以连续的方式组合这两层，即一层接一层...

```
model = tf.sequential()
model.add(input)
model.add(output) 
```

...配置生成的模型。我们通过告诉模型如何确定它做得有多好(`loss`)，告诉它如何改进它(`optimizer`)以及如何以声明的方式向我们报告它做得有多好(`metrics`)来做到这一点。在这种情况下，通过比较用户点击的按钮和模型的预测来确定损失。关于模型的输出`sparseCategoricalCrossentropy`是一种表达方式。`adam`只是优化模型的默认方式，而`accuracy`会报告可正确预测的数据集部分。

```
model.compile({
    loss: "sparseCategoricalCrossentropy",
    optimizer: "adam",
    metrics: ["accuracy"]
}) 
```

## 用用户的点击数据训练我们的模型

我们需要一点预处理和数据转换，这超出了本文的范围，但这会导致所有鼠标位置序列都存储在一个名为`X`的变量中，所有匹配的按钮最终都在变量`y`中被单击。我们将这些数据集在模型中传递 200 次，每一次迭代(也称为 epoch)都有望让模型更好地匹配数据:

```
model.fit(X, y, {
    epochs: 200,
    validationSplit: 0.2,
}) 
```

## 模型实际做的有多好？

不过，还有一个技巧:由于我们真的对模型在以前没有见过的小鼠序列上的表现感兴趣，我们排除了一些已知数据——在我们的例子中是 20%——用于训练过程。相反，我们用它来不断地监控模型的性能。显然，该模型在实际使用中可能表现更差，但这是我们能做的最好的。

按照指标 TensorFlow.js 中的要求，在训练过程中报告训练和验证数据的准确性:

[![Two curves describing accuracy and validation accuracy over time in training](img/1f4d72095866137947bb08d2ea499931.png "Accuracy over time in training")](https://res.cloudinary.com/practicaldev/image/fetch/s--e2zYe0mx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/DJCordhose/ux-by-tfjs/master/img/accuracy-reg.png)

事实上，我们看到了我们所希望的增长，无论是在训练中，还是在验证数据中。根据我们训练的数据在多大程度上代表了用户的实际行为，这可能是一个相当成功的模型。我们已经在本文的第二张图中看到了它在应用程序中的工作原理，我们在这里重复一下:

[![Buttons highlighting when the machine thinks I am going to click them soon](img/9999b3a1c75db38e706147f3c0ff33a1.png "Highlighting in Advance")](https://res.cloudinary.com/practicaldev/image/fetch/s--fMyn9vzA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/DJCordhose/ux-by-tfjs/master/img/simpleRNN.gif)

## 做出预测

我们通过实时反馈用户的实际鼠标运动来使用该模型。除了隐私问题，这是只有 TensorFlow.js 允许我们做的另一件事，因为它直接在浏览器中训练和预测，从而实现所需的响应。这就是我们如何做出这样的预测:

```
probabilityArray = model.predict(X)
y = probabilityArray.argMax(); 
```

在模型上调用`predict`给我们所有现有按钮的概率。对于我们的三个按钮，它们可能是`[0.5087714195251465, 0.31418585777282715, 0.1770426332950592]`。为了匹配我们最初训练的数据，我们只想知道哪个概率最高，并返回一个数字来指示要突出显示的按钮。在这种情况下，应该是`0`。你也可以考虑这些额外的信息，只在你非常确定的时候高亮显示一个按钮，在我们的例子中，我们只在概率超过 50%的时候才这样做。

### 结论

对于手头的任务，只有在浏览器中运行的机器学习才是合适的。这主要是因为

*   适合个人用户和机器
*   隐私
*   实时响应
*   和零安装

作为一个很好的好处，这也可以脱机工作，并且不会给任何服务器带来负担。

这只是在浏览器中使用 TensorFlow.js 的一个用例，但还有更多的用例，我希望您也能使用一个。为了让你开始，看看 https://www.tensorflow.org/js/tutorials 和我放在 https://github.com/DJCordhose/ux-by-tfjs T2 的完整项目。

也看

[https://www.youtube.com/embed/kZ8sXFIJQyg](https://www.youtube.com/embed/kZ8sXFIJQyg)

在 Youtube 上。该视频解释了如何在 http://djcordhose.github.io/ux-by-tfjs/dist 使用演示，它允许你不需要直接在浏览器中安装任何软件就可以自己尝试(尽管需要一个现代的浏览器)。