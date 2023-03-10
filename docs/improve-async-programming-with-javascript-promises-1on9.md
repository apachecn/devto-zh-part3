# 用 JavaScript 承诺改进异步编程

> 原文：<https://dev.to/bnevilleoneill/improve-async-programming-with-javascript-promises-1on9>

[![](img/688f0c0983f70a65a7a4319bace78ad3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7-iKwNM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkyEmj3jmMpX3zhmGgBJ4mQ.jpeg)

如果您以前编写过一些异步 JavaScript 代码，那么您就已经知道如何使用回调以及回调带来的问题。使用回调的一个主要问题是遇到*回调地狱*的可能性。

在 ES6 中，JavaScript 承诺被添加到语言规范中，带来了异步代码编写方式的全新转变，也缓解了遇到*回调地狱*的问题。如果您在代码中使用 ES6 语法，您可能已经熟悉了承诺。

在本指南中，您将看到一些使用 promises 改进 JavaScript 异步编程的实用方法。

本指南绝不是 JavaScript promises 的介绍。阅读本指南需要对承诺有所了解。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 创造承诺

可以使用 promise 构造函数创建 JavaScript promise。构造函数将一个 executor 函数作为它的参数，这个函数被立即执行以创建承诺。

执行程序又可以将两个回调函数作为其参数，这两个函数可以在执行程序函数中调用，以便完成承诺，即:

*   决心实现有价值的承诺
*   拒绝:拒绝承诺并给出原因(通常是错误)

这里有一个非常简单的 JavaScript 承诺，在加载一个图像后解决:

```
const loadPhoto = photo_url =>
  new Promise((resolve, reject) => {
    // Create a new Image object
    const img = new Image();

    img.addEventListener('load', () => {
      // If the image loads,
      // fulfill the promise with the Image object
      resolve(img);
    }, false);

    img.addEventListener('error', () => {
      // If there was an error,
      // reject the promise with an error
      reject(new Error('Image could not be loaded.'));
    }, false);

    // Set the image source
    img.src = photo_url;
  }); 
```

在这里，您可以看到，如果图像加载成功，返回的承诺将通过 img 图像对象来实现。否则，它将被拒绝，并显示一个错误，指示无法加载图像。

### 谈妥承诺

很多时候，你只是想创造一个已经*达成*的承诺(要么*兑现了*的价值，要么*拒绝了*的理由)。对于这种情况，Promise.resolve()和 Promise.reject()方法就派上了用场。下面是一个简单的例子:

```
// This promise is already fulfilled with a number (100)
const fulfilledPromise = Promise.resolve(100);

// This promise is already rejected with an error
const rejectedPromise = Promise.reject(new Error('Operation failed.')); 
```

有时你也不确定一个价值是否是一个承诺。对于这种情况，您可以使用 Promise.resolve()创建一个带有值的*已履行的*承诺，然后处理返回的承诺。这里有一个例子:

```
// User object
const USER = {
  name: 'Glad Chinda',
  country: 'Nigeria',
  job: 'Fullstack Engineer'
};

// Create a fulfilled promise using Promise.resolve()
Promise.resolve(USER)
  .then(user => console.log(user.name)); 
```

通过利用已解决的承诺，您可以像这样修改之前的 loadPhoto()承诺工厂:

```
const loadPhoto = photo_url =>
  new Promise(resolve => {
    const img = new Image();

    img.addEventListener('load', () => { 
      resolve(img);
    }, false);

    img.addEventListener('error', () => {
      const rejection = Promise.reject(new Error('Image could not be loaded.'));
      resolve(rejection);
    }, false);

    img.src = photo_url;
  }); 
```

在这段代码中，有两处明显的变化:

*   首先，promise executor 函数只定义了一个参数，而不是两个。

```
// BEFORE
new Promise((resolve, reject) => {...})

// NOW
new Promise(resolve => {...}) 
```

*   第二，在“错误”事件监听器中，通过解决的承诺(因错误而被拒绝)来履行承诺，而不是通过错误而直接拒绝承诺的前一变体。

```
// BEFORE
reject(new Error('Image could not be loaded.'));

// NOW
const rejection = Promise.reject(new Error('Image could not be loaded.'));

resolve(rejection); 
```

值得注意的是，这两个代码片段仍然以相同的方式工作——这可以归因于在处理已解决的承诺期间创建中间承诺的机制。

### 处理承诺

可以通过将回调链接到承诺的 then()、catch()或 finally()方法来处理已解决的承诺。

下面的代码片段演示了如何处理从我们之前创建的 loadPhoto()函数返回的结算承诺:

```
loadPhoto('https://picsum.photos/g/800/600/?image=0&gravity=north')
  .then(console.log)
  .catch(console.error);

// Alternatively, the rejection can be handled in the same .then() call
// By passing the rejection handler as second argument to .then()
loadPhoto('https://picsum.photos/g/800/600/?image=0&gravity=north')
  .then(console.log, console.error); 
```

由于返回的承诺是用图像对象实现的，所以您可以在。then()处理程序回调，比如向图像添加属性，并将图像追加到文档中。

下面是一个改进的代码片段:

```
loadPhoto('https://picsum.photos/g/800/600/?image=0&gravity=north')
  .then(img => {
    // Set some image attributes
    img.alt = 'Random image from Picsum';
    img.classList = 'img img--fluid';

    // Append the image to the document
    document.body.appendChild(img);
  })
  .catch(console.error); 
```

下面的**代码笔**展示了这个例子的演示:
[https://codepen.io/gladchinda/embed/NOQMdJ?height=600&default-tab=result&embed-version=2](https://codepen.io/gladchinda/embed/NOQMdJ?height=600&default-tab=result&embed-version=2)

#### 哑诡经手人

的。then()方法最多可以接受两个处理函数作为它的参数:分别是*实现处理函数*和*拒绝处理函数*。

但是，如果这两个参数中的任何一个不是函数，则为。然后()用一个函数替换该参数，并继续正常的执行流程。知道参数被什么样的函数替代变得很重要。事情是这样的:

*   如果*履行处理程序*参数不是函数，它将被替换为**标识函数**。一个*标识函数*是一个简单地返回它接收的参数的函数
*   如果*拒绝处理器*参数不是一个函数，它将被替换为一个**抛出函数**。一个*抛出函数*是一个简单地抛出它作为参数接收的错误或值的函数

[![](img/c656df888689c178caf1cc6464abaeec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ypG0CLPN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AOKziwACUUT-zRgbf.png)

如果你仔细观察，你会注意到*身份函数*和*投掷函数*都没有改变承诺序列的正常执行流程。

它们的效果和忽略那个细节是一样的。然后()调用承诺链。出于这个原因，我通常将这些处理程序参数称为“哑处理程序”。

#### 然后经手人回报承诺

有一点很重要。then() promise 方法在于它总是返回一个承诺。

这里是如何分解。然后()根据传递给它的处理函数返回的内容返回一个承诺: