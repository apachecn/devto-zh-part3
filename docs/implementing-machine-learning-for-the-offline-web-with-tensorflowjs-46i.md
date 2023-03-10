# 离线网络的机器学习

> 原文：<https://dev.to/dar5hak/implementing-machine-learning-for-the-offline-web-with-tensorflowjs-46i>

离线网络应用终于成为现实。最聪明的想法以前只是想象，现在变成了现实。

由于现代浏览器提供的各种存储机制，现在可以直接在客户端保存机器学习模型。然后，您可以使用它们来产生输出，而不需要与服务器连接。

这篇文章演示了如何做到这一点。

## 简介

[TensorFlow.js](https://js.tensorflow.org/) 是 Google 支持的开源机器学习库。它让你以类似于它的 Python 对应物 [Keras](https://keras.io) 和[tensor flow](https://www.tensorflow.org)(Python 中的那个)的方式开发和训练神经网络。

在本帖中，我们将使用 TensorFlow.js 的功能将模型保存到浏览器存储中，并使用它进行离线预测。

我们不会从头开始开发一个模型，因为这超出了本文的范围。你可以在 TensorFlow.js 网站上查找优秀的教程。

对于我们的小项目，我们将选择一个已经开发的模型来解决识别手写数字的经典深度学习问题。

## 先决条件

为了能够遵循本指南，您需要具备以下基本知识:

*   节点. js
*   围绕神经网络的一般概念，如训练和一键编码

此外，您需要在机器上安装以下软件:

*   node . js(8 . 15 . 0 版或更高版本)
*   Git (optional)
*   任何现代浏览器

准备好了吗？让我们开始吧。

## 第一步:训练模型

Tensorflow.js 社区提供了数字识别神经网络的一个很好的[实现](https://github.com/tensorflow/tfjs-examples/tree/master/mnist-node)。它使用著名的 MNIST 数据集进行训练。我们将获得源代码，并自己训练这个模型。如果您不想这样做，可以跳到步骤 1a。

转到 GitHub 上的 [tensorflow/tfjs-examples](https://github.com/tensorflow/tfjs-examples) 资源库，将其克隆或下载到您的机器上。

导航到`tfjs-examples/mnist-node`目录。使用以下命令安装依赖项:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

接下来，运行以下命令来训练模型并保存其文件:

```
node main.js --model_save_path=./models 
```

Enter fullscreen mode Exit fullscreen mode

这些将需要几分钟(或几分钟)来运行，这取决于您的硬件。一旦完成，您将在`mnist-node`下看到一个新的`models`目录。它将有两个文件:

*   `model.json`是神经网络的编译结构。它包含每层的大小、形状和配置等信息。
*   `weights.bin`顾名思义，包含了训练网络后分配给各个节点的权重。

### 步骤 1a:下载预训练模型(可选)

如果您不想自己训练模型，可以从我的存储库中下载预训练文件:

*   [model.json](https://raw.githubusercontent.com/dar5hak/offline-mnist/master/static/models/model.json)
*   [weights.bin](https://github.com/dar5hak/offline-mnist/blob/master/static/models/weights.bin?raw=true)

## 第二步:Web 应用程序设置

在其他地方为你的 Web 应用程序源代码创建一个单独的目录。姑且称之为`offline-mnist`。

要告诉大家我们将使用 npm 依赖项，请在您的`offline-mnist`目录中运行这个命令:

```
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

这将生成一个`package.json`文件。

既然我们想在代码中使用 TensorFlow.js，我们就把它声明为一个依赖:

```
npm install @tensorflow/tfjs 
```

Enter fullscreen mode Exit fullscreen mode

这也是您可能希望安装项目所需的任何开发依赖项的地方。我在 package bundler 中使用了 TypeScript，所以我不得不做如下的事情:

```
npm install --save-dev typescript parcel-bundler parcel-plugin-static-files-copy 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:复制模型

复制您在步骤 1 中创建的`models`目录，并将其粘贴到您的项目目录中，在一个新的`static`子目录中(或者您的 bundler 寻找静态资产的任何地方)。

这将确保您的训练模型文件可供浏览器下载。

## 第四步:使用 HTTP 加载模型

厉害！既然样板文件已经完成，是时候添加一些令人兴奋的代码了。

创建一个`index.js`文件(如果选择了 TypeScript，则为`index.ts`)。

首先:

```
import { loadLayersModel } from "@tensorflow/tfjs"; 
```

Enter fullscreen mode Exit fullscreen mode

`loadLayersModel`函数允许您从各种来源获取 TensorFlow.js 模型——在我们的例子中是 HTTP。它返回模型对象的一个`Promise`。

我们需要提供一个到`loadLayersModel`的 URL，告诉它从哪里获取文件。如果它以`http://`或`https://`开头，它将知道它需要进行一个 HTTP 调用。

因为我们提供来自同一个起点的所有东西，所以我们将使用`window.location.href`来确定当前的起点，这可能类似于`http://127.0.0.1:1234/`。

```
const MODEL_HTTP_URL = "models/model.json";

async function fetchModel() {
  try {
    const model = await loadLayersModel(window.location.href + MODEL_HTTP_URL);
    console.log("Model loaded from HTTP.");
    console.log(model);
    return model;
  } catch (error) {
    console.error(error);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`model`对象现在可以使用了。

## 第五步:将模型保存到 IndexedDB

既然您已经有了可用的模型对象，那么要做的第一件事就是将它保存到浏览器存储中。我们将使用的存储机制称为 IndexedDB。

```
const MODEL_HTTP_URL = "models/model.json";
const MODEL_INDEXEDDB_URL = "indexeddb://mnist-model";

async function fetchModel() {
  try {
    const model = await loadLayersModel(window.location.href + MODEL_HTTP_URL);
    console.log("Model loaded from HTTP.");

    // Store the downloaded model locally for future use
    await model.save(MODEL_INDEXEDDB_URL);
    console.log("Model saved to IndexedDB.");

    return model;
  } catch (error) {
    console.error(error);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你看一下我们用来保存它的 URL，你会看到它是以`indexeddb://`开头的。这告诉 TensorFlow.js 在哪里存储它。

## 第六步:放在一起

好了，现在我们有一个快速的离线方法来得到我们的模型。因此，对于任何后续的页面加载，我们应该总是从 IndexedDB 加载，对吗？

不完全是。

当然，我们想要速度，所以我们应该更喜欢 IndexedDB，但要记住它不是 100%可靠的。

在以下任何情况下，您保存的数据可能不再可用:

*   用户正在私人/匿名模式下浏览
*   用户清除站点数据或他们的整个浏览器数据
*   当设备存储空间不足时，浏览器决定释放空间

在这种时候，旧的 HTTP 仍然可以拯救我们。

我们首先尝试从 IndexedDB 获取模型，因为这样更快，但是如果这样做失败了，我们就从 HTTP 获取模型并再次保存到 IndexedDB。

```
async function fetchModel() {
  try {
    // Try loading locally saved model
    const model = await loadLayersModel(MODEL_INDEXEDDB_URL);
    console.log("Model loaded from IndexedDB");

    return model;
  } catch (error) {
    // If local load fails, get it from the server
    try {
      const model = await loadLayersModel(
        window.location.href + MODEL_HTTP_URL
      );
      console.log("Model loaded from HTTP.");

      // Store the downloaded model locally for future use
      await model.save(MODEL_INDEXEDDB_URL);
      console.log("Model saved to IndexedDB.");

      return model;
    } catch (error) {
      console.error(error);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那里！漂亮又结实！

## 第七步:做预测

现在我们的函数已经准备好了，是时候好好利用它了。

这里我们需要的是模型上的`predict`方法。它接受一个[张量](https://js.tensorflow.org/tutorials/core-concepts.html)数据类型作为输入。

简而言之，张量是固定大小和固定数据类型的 n 维数组。

我们的数字识别模型被设计成接受四维张量作为输入。张量的形状需要是`[1, 28, 28, 1]`。这意味着第一维的长度为`1`，第二维的长度为`28`，依此类推。

输出也是一个张量，它的第二维是一个独热编码的预测数组。我们可以在这个维度上使用`argMax`来确定结果。

将所有这些信息翻译成代码将导致:

```
async function predict(input, model) {
  const prediction = model.predict(input.reshape([1, 28, 28, 1]));
  const result = await prediction.argMax(1).data();
  return result[0];
} 
```

Enter fullscreen mode Exit fullscreen mode

这种情况下的输入是用户在网页上绘制的数字，转换成张量类型。如何做到这一点，又是一个很长的故事，但你可以随时参考我的库。

对于现场演示，请查看[我的实现](https://dar5hak.github.io/offline-mnist/)。

## 结论

我们想离线进行神经网络预测。为此，我们训练了一个模型，并将其从 HTTP 提取到我们的 Web 应用程序中。然后我们将它存储到 IndexedDB 中以备后用。

在随后的每次调用中，我们都试图从 IndexedDB 加载模型，从而节省了一次网络调用，如果失败，我们就退回到从 HTTP 再次获取它。

这是进行预测的最简单的用例，我希望你现在可以开始离线优先机器学习的更高级的应用。

不断学习，让你的机器不断学习。

### 更新 2019-08-04

更改代码以使用 TensorFlow.js 1.x。