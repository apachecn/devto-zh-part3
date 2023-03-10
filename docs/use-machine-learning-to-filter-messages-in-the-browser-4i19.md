# 使用机器学习在浏览器中过滤消息

> 原文：<https://dev.to/embiem/use-machine-learning-to-filter-messages-in-the-browser-4i19>

我们将使用人工神经网络来将消息分类为“垃圾邮件”或“非垃圾邮件”。

目标是收拾这个烂摊子:

[![Twitch Chat Mess](img/07dd6919175b4264116c517c7c045bdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yygUhy7---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6g3f7y7t9kvw2t5ro8sw.png)

## 问题

> "该消息应该隐藏还是显示？"

所有的解决方案都是从问题陈述开始的。在我们的例子中，我们有一个文本消息，并希望将其分类为“垃圾邮件”或“非垃圾邮件”。

这个问题的经典应用应该是电子邮件，但我选择使用大频道的 Twitch 聊天。这些往往是垃圾邮件，因此很难阅读，但很容易获得数据。

### 什么是抽动？

Twitch.tv 是一个在线平台，可以播放视频，并与互联网上成千上万志同道合的人联系。它最常用于视频游戏和玩游戏的人的视频流。聊天是与流媒体工具交互的主要窗口，但特别是在有很多观众的频道中，聊天可能会变得非常繁忙，并且由于垃圾消息的低价值而无法阅读。

## 解

将短信过滤成垃圾短信和非垃圾短信是一个*二元分类*问题。有很多方法可以解决这个问题，我选择了一个:人工神经网络。

更具体地说，它将是一个具有反向传播的前馈神经网络。这是一个非常常见和基本的设置，其中我们的节点不会形成循环(因此是前馈)，所有节点都将被通知它们的表现如何(因此是反向传播)。

如果你想直接跳到代码，这里有回购。请半信半疑地接受这一切(这是正确的说法，不是吗？).这是我两年前创造的，作为我在 Udacity 的机器学习纳米学位的一部分。从那以后，很多事情都发生了变化，比如发布了 [TensorFlow.js](https://js.tensorflow.org) 。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[embiem](https://github.com/embiem)/[Better-Twitch-Chat](https://github.com/embiem/Better-Twitch-Chat)

### 使用 Twitch 聊天，ML，Twitch-北海巨妖 API，reactJS 和 firebase 的爱好项目

<article class="markdown-body entry-content container-lg" itemprop="text">

一个爱好项目，试图为 Twitch PoC 实现一个更舒适且功能丰富的聊天应用程序，该应用程序是在 Udacity 的机器学习 Nanodegree 的顶点项目期间开发的。

这个项目是用 [Create React App](https://github.com/facebookincubator/create-react-app) 引导的，它的指南可以在[这里](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md)找到。

## 第三方库

*   brain.js -用于使用神经网络将消息分类为“显示”或“隐藏”
*   firebase——通过 Twitch 持久化数据和认证用户
*   材料-用户界面-简单快速的用户界面/UX
*   mimir 词袋模型
*   react -前端框架< 3
*   冗余状态< 3
*   redux-thunk -对于那些实际上不是行动的可怕行动
*   tmi.js - Twitch api 和聊天连接

## 新克隆

基本按照这里的步骤来。

1.  用 firebaseConfig & twitchConfig 创建`src/config.js`文件。
2.  创建`functions/service-account.json`文件[文档](https://firebase.google.com/docs/admin/setup#add_firebase_to_your_app)
3.  将 twitch clientId & clientSecret 设置为 GCloud env 变量 [Docs](https://github.com/firebase/functions-samples/tree/master/instagram-auth)
4.  `firebase functions:config:set twitch.client_id="yourClientID" twitch.client_secret="yourClientSecret"`
5.  对于本地开发，还要设置`firebase functions:config:set twitch.client_id_dev="yourClientDevID" twitch.client_secret_dev="yourClientDevSecret"`

## 桌子

…</article>

[View on GitHub](https://github.com/embiem/Better-Twitch-Chat)

还有一个在线版本的[网络应用](https://better-twitch-chat.firebaseapp.com)，它可以让你连接到一个 Twitch 频道，根据喜欢/不喜欢的信息来过滤它或训练你自己的模型。

看完了吗？太好了，我们继续...

### 安是什么？

人工智能、机器学习、深度学习以及类似的流行语现在很热。所以让我们确保术语是清楚的:深度学习是机器学习的子集，人工智能是一个研究领域，其中*可能*使用机器学习。我总是喜欢举 [Boston Dynamics 的 Atlas 机器人](https://www.youtube.com/watch?v=fRj34o4hN4I)为例:它是 AI 和机器人研究的产物，但[实际上并没有使用任何机器学习解决方案。](https://youtu.be/lXZ6y3lMymM?t=311)

通过 [ANN](https://en.wikipedia.org/wiki/Artificial_neural_network) ，我们描述了在生物[神经网络](https://en.wikipedia.org/wiki/Neural_network)之后建模数字系统的一般概念。我们的大脑由许多相互连接的神经元组成，这些神经元形成了神经网络。我们知道它们是如何工作的:[神经元接收输入，如果输入高于阈值](https://www.psychologynoteshq.com/neurons-what-are-they-and-how-do-they-work/)，则可能发送输出。

一个*人工*神经网络由模仿神经元行为的节点组成。就像乐高积木一样，我们以特定的方式组装它们，形成一些很酷的东西，比如千年隼

[![Lego Millennium Falcon](img/589439a3b960fed5b268fa1787a31941.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AloAEJUo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bcpn55x7tjv7elxk9hxx.jpg)

玩笑归玩笑，看到精心构建的神经网络架构能够实现这样的事情是非常令人惊讶的:

[https://www.youtube.com/embed/dbQh1I_uvjo](https://www.youtube.com/embed/dbQh1I_uvjo)

最终，一切都归结于节点。以特定的方式连接并附加一些逻辑，这定义了如何使用输入来创建输出。

### 安是如何工作的？

让我们以我们的具有反向传播的前馈神经网络为例。它应该将收到的文本分类为“垃圾邮件”或“非垃圾邮件”。我们将*输入我们的文本消息*到我们网络中的第一个节点。这些节点将根据接收到的*输入*和节点的*内部状态*进行一些*计算*。然后，结果被发送到下一个节点。这种情况一直发生，直到我们到达网络中的最后一个节点，该节点将负责*将输入的文本消息分类为*是“垃圾邮件”还是“非垃圾邮件”。在训练过程中，我们知道一个消息是否是垃圾邮件，并会给网络一个👍或者👎，基于它的表现。该反馈通过网络的所有节点传播回来，并且每个节点将稍微调整其内部状态。

有一个很酷的游戏工具，可以让你在你的浏览器中训练一个神经网络，并尝试不同的部分:[playground.tensorflow.org](https://playground.tensorflow.org)。不要太担心什么是学习率或 TanH 激活。只是玩一下。玩得开心🤓

再者，在写这篇文章的同时， [@Petro Liashchynskyi](https://dev.to/liashchynskyi) 发表了这篇文章，在更技术的层面上解释了 ann 及其概念。

[![liashchynskyi image](img/798c4805bbf6d5b455756b3252142452.png)](/liashchynskyi) [## 使用 JavaScript 在 7 分钟内创建神经网络！

### petro liashchynskiy 1 月 12 日 194 分钟阅读

#javascript #webdev #neuralnetworks #machinelearning](/liashchynskyi/creating-of-neural-network-using-javascript-in-7minutes-o21)

在这篇文章的最后，我还会列出一些很棒的 ML 资源。

## 数据

任何机器学习解决方案最重要的依赖是数据。数据越多越好，模型的表现就越好。

### 数据采集

这通常是最困难的任务之一。试想一下，[给几百万张图片](https://storage.googleapis.com/openimages/web/index.html)贴上“苹果”、“自行车”、“人”、“狗”的标签，...

在([监督的](https://youtu.be/OpodKCR6P-M?t=146))机器学习中，机器通过实例进行学习。所以我们需要给机器很多例子。任务越复杂(比如对图像中的对象进行分类)，我们需要的例子就越多。

对于我们的问题，我们需要大量的文本消息，并将它们标记为“垃圾邮件”或“非垃圾邮件”。我选择过滤 Twitch 频道的消息的原因之一是:大多数消息都很小，收集数据相对容易。

在[网络应用](https://better-twitch-chat.firebaseapp.com)中，有一个视图专门用于收集数据。你加入一个 Twitch 频道，所有消息都会实时列出，就像内置的 Twitch 聊天一样。此外，每封邮件都有一个“喜欢/不喜欢”按钮，用于指示邮件是否是垃圾邮件。所有喜欢/不喜欢的消息都被发送到数据库。

### 数据预处理

机器和人类之间的一个主要区别是:他们在阅读和计算 0 和 1 方面非常高效，而我们在理解概念方面非常出色。我们看到一堆信，阅读它们，理解它们所保留的信息。机器甚至不知道字母是什么。

这就是为什么我们人类建立了像 UTF-8 这样的字符编码，这给了机器一种构建 0 和 1 的方法，以形成字母的概念。然后我们可以做一些事情，比如将多个字符放入一个数组中来构建句子，或者将像这样的文章保存在数据库中。

为了教会机器关于我们世界的任何事情，我们需要记住这些事情。在这个项目中，我们处理文本，那么我们如何将这些文本输入到 ML 模型中呢？

有一种流行的解决方案叫做[单词包(BOW)](https://github.com/techfort/mimir#bow) ，它将文本作为输入，输出一堆 0 和 1。完美！

对于这个项目，你可以在 [`node/dataPrep.js`](https://github.com/embiem/Better-Twitch-Chat/blob/master/node/dataPrep.js) 的一个小文件中找到数据预处理。让我们在这篇文章中加入一些代码。

首先，我们需要将我们的[数据集](https://github.com/embiem/Better-Twitch-Chat/blob/e98438429ea7d95537810d71d6de246bb111f3e7/node/btc_messages-dataset.json)放入一个数组，并将所有邮件和标签(show = "no spam" & hide = "spam ")放入另一个数组:

```
const dataFlat = [];
const dataLabels = [];

for (let key in data) {
  dataFlat.push(data[key].message);
  dataLabels.push(data[key].liked ? 'show' : 'hide');
} 
```

然后我们创建一个单词字典，使用 [mimir](https://github.com/techfort/mimir) :

```
const dictData = mimir.dict(dataFlat);
const dictLabels = mimir.dict(dataLabels); 
```

最后，我们通过删除数据集中只出现过一次的条目来优化我们的数据:

```
for (let key in dictData.dict) {
  if (dictData.dict[key] < 2) {
    delete dictData.dict[key];
    _.remove(dictData.words, w => w === key);
    _.remove(dataFlat, w => w === key);
  }
} 
```

这是可选的，但是会降低我们的模型需要处理的复杂性。BOW 字典越大，输入空间就越复杂。也叫:[维度的诅咒](https://en.wikipedia.org/wiki/Curse_of_dimensionality)。

### 数据探索&可视化

由于数据对您的 ML 解决方案的成功非常重要，了解您的数据是其中的一部分。如果不知道数据是如何构造的，也没有找到某些特征，甚至很难开始开发一个模型。这对于不是您创建的数据集尤其重要。

对于我们的项目，我们非常了解数据，因为我们实际上是通过喜欢/不喜欢消息来创建数据集的。一个有趣的视觉化现象是单词 occurrence。下图显示了在标记为“显示”/“无垃圾邮件”的邮件中出现次数最多的单词:

[![Word Occurrence Graph](img/b55f526786645bea5768ef0aba1f8110.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Xs75OFH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3otvxgtjol4tugwxnac.png)

很普通的词，对吧？有趣的是，这些邮件出现在被标记为“垃圾邮件”的邮件中的频率也是如此。这很有趣，因为它们经常出现在“非垃圾邮件”消息中，但几乎从不出现在“垃圾邮件”消息中。因此，在对包含这些单词的消息进行分类时，它们对 ML 模型的性能有很大的影响。

当试图降低维数时，这些信息也会派上用场。某些方法试图找到高熵特征并组合这些特征。但那是另一篇文章的内容了。

即使你不懂 python，Kaggle 上也有一本关于数据探索的非常不错的笔记本:[用 Python 进行全面的数据探索](https://www.kaggle.com/pmarcelino/comprehensive-data-exploration-with-python)。

对于 JavaScript 开发人员，您可能想看看 Observable。就看看泰坦尼克号数据集的这个[数据探索 JS 笔记本吧。挺爽的！](https://beta.observablehq.com/@jjimenez/kaggle-titanic-data-exploration)

## 型号

简单的部分来了！

一旦你知道了你的数据和你想要达到的目标，就该创建模型了。

### 建立你的模型

我没开玩笑。在我看来，数据探索实际上耗费了你更多宝贵的脑汁。构建和训练你的模型“只是”需要时间和迭代。

你可以看看研究论文或者一份经过验证的网络架构列表，但是这有什么意思呢？

所以让我们建立我们的模型:

```
const net = new brain.NeuralNetwork({
  hiddenLayers: layers,
  learningRate: lr
}); 
```

我说这很容易！

这并不意味着它不能把[复杂化](https://github.com/llSourcell/Neural_Differential_Equations/blob/master/Neural_Ordinary_Differential_Equations.ipynb)。只是没必要。尤其是使用 brain.js、tf.js、keras 等工具。，只用几行代码就可以实现一个性能良好的模型。这当然总是取决于你想解决的问题。

### 模型训练

这部分实际上可能会更复杂一点。我的计划是用不同的架构和参数训练多个网络。所以我写了 [`node/modelFactory.js`](https://github.com/embiem/Better-Twitch-Chat/blob/master/node/modelFactory.js) ，封装了模型的构建和训练。这允许我用不同的层和学习速率来训练多个模型。

*学习速率*定义了模型学习的速度。有一些很好的默认值，但是有些型号在 LR 较高的情况下工作得更好，而有些型号在 LR 较低的情况下工作得更好。这张流行的图表显示了 LR 过高或过低会发生什么:

[![LR too high or low](img/5adb91e62fa942fe745cb83fd1d4ccf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pQcf99Xf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rzq2clmlf68zsfzngcxz.png)

在深入研究`modelFactory.js`代码之前，这里是模型训练的基本部分。

首先，您可能需要转换您的数据，这个项目就是如此。我们用 mimir 建立了字典，现在我们需要得到真正的单词包。这将返回独热编码数组(例如[0，1，0，0，1])。重要的是，这些数组的长度总是相同的。每一个 1 对应于字典中的某一个单词，意味着这个蝴蝶结所代表的信息包含相应的单词。查看 [mimir 的 BOW 代码示例](https://github.com/techfort/mimir#bow)。

```
const allData = [];
dataFlat.forEach((entry, idx) => {
  allData.push({
    input: mimir.bow(entry, dictData),
    output: mimir.bow(dataLabels[idx], dictLabels)
  });
}); 
```

然后，我们使用我们当前的`layer`和`lr`参数来构建模型，并用我们的训练数据来训练它。

```
 // create the net
        const net = new brain.NeuralNetwork({
          hiddenLayers: layers,
          learningRate: lr
        });

        // train the net
        const trainResult = net.train(traindata, {
          errorThresh: 0.005,
          iterations: 10000,
          log: true,
          logPeriod: 100
        });

        // test the net
        const testResult = testing(net, testdata, threshold);

        // write net to file
        fs.writeFileSync(
          `./out/nets/${fold + 1}_${netName}.json`,
          JSON.stringify(net.toJSON())
        ); 
```

这将保存一个训练好的模型，可以随时使用。

### 模型评估

你看到上面代码片段中的`testing(net, testdata, threshold)`调用了吗？这将测试我们的模型在训练后的表现。

它计算精确度、召回率，并最终计算 F1 分数。这是一个常用的分数，在我们的二元分类项目中特别有用。

实现非常简单:

```
function(net, testData, threshold) {
  const maxarg = array => {
    return array.indexOf(Math.max.apply(Math, array));
  };

  let truePositives = 0;
  let trueNegatives = 0;
  let falsePositives = 0;
  let falseNegatives = 0;

  for (let i = 0; i < testData.length; i++) {
    const result = net.run(testData[i].input);
    if (result.show >= threshold && testData[i].output.show === 1) {
      truePositives++;
    } else {
      falseNegatives++;
    }
  }

  const precision = truePositives / (truePositives + falsePositives);
  const recall = truePositives / (truePositives + falseNegatives);
  const f1 = 2 * ((precision * recall) / (precision + recall));
  return { precision, recall, f1 };
}; 
```

我们采用经过训练的网络、一些测试数据和一个手动设置的阈值。阈值是我们想要将某个东西分类为“垃圾邮件”或“非垃圾邮件”的界限。一个合理的值应该是`0.8`，这意味着如果模型 80%确定一个消息是“无垃圾邮件”，那么我们将把它分类为“无垃圾邮件”，否则为“垃圾邮件”。

这个分数然后被用来评估某个模型的表现如何。你可以看到我在 [`node/hyperparamTuning.js`](https://github.com/embiem/Better-Twitch-Chat/blob/master/node/hyperparamTuning.js) 测试了很多不同的模型架构。

## 使用模型对消息进行分类

这部分用 brain.js 轻而易举，模型可以用 JSON 字符串表示。同样的事情也适用于使用 mimir 的单词字典。所以我们需要做的就是从后端以字符串的形式加载模型和词典。

React web app 中所有的 ML 代码都位于 [`src/api/NeuralNet.js`](https://github.com/embiem/Better-Twitch-Chat/blob/master/src/api/NeuralNet.js) 。本质上，要预测一条新消息是否是垃圾邮件，我们只需要调用:

```
 predict(message) {
    const maxarg = array => {
      return array.indexOf(Math.max.apply(Math, array));
    };

    if (typeof message !== 'string' || message.length < 1) {
      console.warn(`Invalid message for prediction: ${message}`);
      return 0;
    }

    if (!this.net || !this.dict || typeof this.net.run !== 'function') {
      console.error('Cant predict because: net | dict', this.net, this.dict);
      return 0;
    }

    const test_bow_message = mimir.bow(message, this.dict);
    const prediction = this.net.run(test_bow_message);
    return maxarg(prediction);
  } 
```

这将...

*   创建传入消息的 BOW 表示
*   在神经网络上调用`.run()`得到一个预测，是这样一个数组:`[0.2, 0.8]`
*   返回具有最高值的预测数组的索引，以将邮件分类为“垃圾邮件”或“非垃圾邮件”

就是这样！我们成功地使用 NodeJS 离线训练了一个模型，将表现最好的模型保存为 JSON，并在 web 应用程序中使用它来预测新消息。

我忽略了 web 应用程序中的一些内容。这些包括数据集的创建、实时数据收集和浏览器内培训。你可以在[回购](https://github.com/embiem/Better-Twitch-Chat)中找到所有这些功能，并在[网络应用](https://better-twitch-chat.firebaseapp.com/)中测试它们。如果您希望另一篇文章更深入地介绍某些功能，请告诉我。

## 更多阅读&观看约 ML

*   关于用 JavaScript 构建神经网络的免费课程。需要一个下午:[JavaScript 中的神经网络](https://scrimba.com/g/gneuralnetworks)
*   带说明的神经网络架构列表:[最完整的神经网络图表，已说明](https://towardsdatascience.com/the-mostly-complete-chart-of-neural-networks-explained-3fb6f2367464)
*   12 为初学者讲解神经网络的视频:[神经网络初学者入门](https://www.youtube.com/watch?v=ZzWaow1Rvho&list=PLxt59R_fWVzT9bDxA76AHm3ig0Gg9S3So)
*   Tensorflow.js 识别手写数字教程:[对图像的训练:用卷积神经网络识别手写数字](https://js.tensorflow.org/tutorials/mnist.html)
*   丹尼尔·希夫曼关于遗传算法的视频系列。[遗传算法——代码的本质](https://www.youtube.com/watch?v=9zfeTw-uFCw&list=PLRqwX-V7Uu6bJM3VgzjNV5YxVxUwzALHV)

## 后记

我绝不是这方面的专家，但我尝试过机器学习，并希望鼓励更多的(web)开发人员也尝试一下。希望这篇文章能帮助你们中的一些人做到这一点！

也感谢本·哈尔彭，他鼓励我写这篇文章。我真的很喜欢 dev.to 社区，你们太棒了！❤️