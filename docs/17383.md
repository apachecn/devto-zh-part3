# brain . js-JavaScript 中的神经网络

> 原文：<https://dev.to/ben/brainjs---neural-networks-in-javascript-4dj6>

这是一个相当了不起的项目，它提供了一个用 JavaScript 编写的神经网络库。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[brain js](https://github.com/BrainJS)/[brain . js](https://github.com/BrainJS/brain.js)

### 🤖用于浏览器和 Node.js 的 JavaScript 中的 GPU 加速神经网络

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Logo](img/17cb221c6b6c477d74da343b94d1c2d4.png)](https://camo.githubusercontent.com/4eef7ab6d511ee1f898406e9afd7cdb6f6120521402b6c423fb75ec0ca5658d1/68747470733a2f2f63646e2e7261776769742e636f6d2f686172746875722d6f72672f627261696e2e6a732f66663539353234322f6c6f676f2e737667)

# brain.js

用于浏览器和 Node.js 的 JavaScript 中的 GPU 加速神经网络

[![GitHub](img/b20b0d9ccb5c8a72f6c0e500566cbb47.png)](https://brain.js.org)[![npm](img/192bd097f823326d323b62e606dc2ee2.png)](https://npmjs.com/package/brain.js)[![js-standard-style](img/a80471d1f86a2a02377083d7ac12d11e.png)](http://standardjs.com)[![Backers on Open Collective](img/d957692ad316229d9dde6077fda181c7.png)](https://github.com/BrainJS/brain.js#backers)[![Sponsors on Open Collective](img/be6c0db2327e36ba0a8273026a8ee2eb.png)](https://github.com/BrainJS/brain.js#sponsors)[![Gitter](img/80064cbae208bbac11d2f49a393a38ef.png)](https://gitter.im/brain-js/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)[![Slack](img/f778bba389e24139f700e97138a2359d.png)](https://slack.bri.im)[![CI](img/20483d879355b79df4e10e2eb005cc23.png)](https://github.com/BrainJS/brain.js/workflows/CI/badge.svg)[![codecov](img/3e76957f62ee46415f84953ac03329f7.png)](https://codecov.io/gh/BrainJS/brain.js)[![Twitter](img/e4c97de361abf50a26534a5a070abaa6.png)](https://twitter.com/brainjsfnd)

[![NPM](img/ef5163fa53211c7e4fe3606d3b9bf280.png)](https://nodei.co/npm/brain.js/)

## 关于

`brain.js`是用 JavaScript 编写的用于[神经网络](http://en.wikipedia.org/wiki/Artificial_neural_network)的 GPU 加速库。

<g-emoji class="g-emoji" alias="bulb" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4a1.png">💡</g-emoji>这是 [**harthur/brain**](https://github.com/harthur/brain) 的延续，不再维护了。[更多信息](https://github.com/harthur/brain/issues/72)

## 目录

*   [安装和使用](https://github.com/BrainJS/brain.js#Installation-and-Usage)
    *   [NPM](https://github.com/BrainJS/brain.js#NPM)
    *   [CDN](https://github.com/BrainJS/brain.js#CDN)
    *   [下载](https://github.com/BrainJS/brain.js#Download)
    *   [安装注意事项](https://github.com/BrainJS/brain.js#Installation-note)
    *   [从源构建](https://github.com/BrainJS/brain.js#Building-from-source)
*   [例题](https://github.com/BrainJS/brain.js#examples)
    *   [更多例子](https://github.com/BrainJS/brain.js#more-examples)
*   [训练](https://github.com/BrainJS/brain.js#training)
    *   [数据格式](https://github.com/BrainJS/brain.js#data-format)
        *   [用于神经网络训练](https://github.com/BrainJS/brain.js#for-training-with-neuralnetwork)
        *   [用`RNNTimeStep`、`LSTMTimeStep`和`GRUTimeStep`、](https://github.com/BrainJS/brain.js#for-training-with-rnntimestep-lstmtimestep-and-grutimestep)进行训练
        *   [用`RNN`、`LSTM`和`GRU`、](https://github.com/BrainJS/brain.js#for-training-with-rnn-lstm-and-gru)进行训练
    *   [培训选项](https://github.com/BrainJS/brain.js#training-options)
    *   [异步训练](https://github.com/BrainJS/brain.js#async-training)
    *   [交叉验证](https://github.com/BrainJS/brain.js#cross-validation)
    *   [列车流](https://www.npmjs.com/package/train-stream)
*   [方法](https://github.com/BrainJS/brain.js#methods)
    *   [列车](https://github.com/BrainJS/brain.js#traintrainingdata---trainingstatus)
    *   [运行](https://github.com/BrainJS/brain.js#runinput---prediction)
    *   [预测](https://github.com/BrainJS/brain.js#forecastinput-count---predictions)
*   [失败](https://github.com/BrainJS/brain.js#failing)
*   [JSON](https://github.com/BrainJS/brain.js#json)
*   [独立功能](https://github.com/BrainJS/brain.js#standalone-function)
*   [选项](https://github.com/BrainJS/brain.js#options)
    *   [激活](https://github.com/BrainJS/brain.js#activation)
    *   [隐藏玩家](https://github.com/BrainJS/brain.js#hiddenlayers)
*   [溪流](https://github.com/BrainJS/brain.js#streams)
    *   [例子](https://github.com/BrainJS/brain.js#example)
    *   [初始化](https://github.com/BrainJS/brain.js#initialization)
    *   [变换](https://github.com/BrainJS/brain.js#transform)
*   [实用程序](https://github.com/BrainJS/brain.js#utilities)
    *   [T2`likely`](https://github.com/BrainJS/brain.js#likely)
    *   [T2`toSVG`](https://github.com/BrainJS/brain.js#toSVG)
*   [神经网络类型](https://github.com/BrainJS/brain.js#neural-network-types)
    *   [为什么神经网络类型不同？](https://github.com/BrainJS/brain.js#why-different-neural-network-types)

## 安装和使用

### NPM

如果可以用 [npm](http://npmjs.org) 安装`brain.js`:

```
npm install brain.js
```

Enter fullscreen mode Exit fullscreen mode

### 加拿大

```
<script src="//unpkg.com/brain.js"></script>
```

Enter fullscreen mode Exit fullscreen mode

### [计] 下载

[为浏览器下载最新的 brain.js】](https://unpkg.com/brain.js)

### 安装说明

`Brain.js` …

</article>

[View on GitHub](https://github.com/BrainJS/brain.js)

这里有一个例子，展示了如何近似自述文件中的 XOR 函数:

```
// provide optional config object (or undefined). Defaults shown.
const config = {
    binaryThresh: 0.5,
    hiddenLayers: [3],     // array of ints for the sizes of the hidden layers in the network
    activation: 'sigmoid'  // supported activation types: ['sigmoid', 'relu', 'leaky-relu', 'tanh'],
    leakyReluAlpha: 0.01   // supported for activation type 'leaky-relu'
};

// create a simple feed forward neural network with backpropagation
const net = new brain.NeuralNetwork(config);

net.train([{input: [0, 0], output: [0]},
           {input: [0, 1], output: [1]},
           {input: [1, 0], output: [1]},
           {input: [1, 1], output: [0]}]);

const output = net.run([1, 0]);  // [0.987] 
```

Enter fullscreen mode Exit fullscreen mode

它得到了积极的维护。绝对值得一试。

这篇文章是新的 GitHunt DEV 标签的一部分。像这样继续下去。

## # [githunt](https://dev.to/t/githunt) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:18442,&quot;name&quot;:&quot;githunt&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: githunt" aria-pressed="false">跟随</button>

Interesting open source repos you've discovered and want to share with the community.