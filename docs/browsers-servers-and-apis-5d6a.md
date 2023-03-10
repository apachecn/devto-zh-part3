# 浏览器、服务器和 API

> 原文：<https://dev.to/johnhenry/browsers-servers-and-apis-5d6a>

*最初发布:[https://medium . com/@ iamjohnhenry/browsers-servers-and-APIs-2f7b 10523 f 39](https://medium.com/@iamjohnhenry/browsers-servers-and-apis-2f7b10523f39)T3】*

(或者“同构 Javascript 之路”)

注意:本文中的例子是使用[节点](https://nodejs.org/en/)和[浏览器](http://browserify.org/)测试和运行的。

如果你是一个现代 JavaScript 爱好者，你应该熟悉同构代码——你可以编写在浏览器和节点环境下运行的代码。因为 JavaScript 是一种标准化语言，您可能认为这是一件小事。问题不在于语言本身，而在于 [API](https://en.wikipedia.org/wiki/Application_programming_interface) s，它们特定于每个环境，并且没有标准化以与其他环境很好地配合。

一个环境中的一些动作在另一个环境中没有意义，因此它们缺少特定的 API。浏览器无权访问文件系统，因此 node 附带的 [fs 对象](https://nodejs.org/api/fs.html)在这里没有意义。相反，当我年轻的时候，我会大量使用[窗口。警报](https://developer.mozilla.org/en-US/docs/Web/API/Window/alert)进行调试，但是当你查看命令行时，弹出窗口不会出现。

但是，有些操作既可以在节点中完成，也可以在浏览器中完成。其中最有趣的是创建服务器。

## **标准化服务器**

传统上，您不会在浏览器中设置服务器，但是考虑到服务器的作用——异步接受请求和响应，我们可以想到许多用例。

例如，服务人员中的服务器将能够在浏览器中拦截和响应请求，而不必通过网络进行呼叫。

```
//example -- service worker
const server = …;//define later

self.addEventListener(‘fetch’, function(event) {
 return event.respondWith(new Promise((resolve, reject)=>{
   const response = server.respondTo(event.request);
   response.on(“finish”, ()=>{
     resolve(req);
   });
 }));
}); 
```

我们会回到这个想法...

## **参赛标准**

所以，我想在浏览器中放置一个服务器，我有什么选择呢？不久前，我了解到 [Mozilla](https://blog.mozilla.org/) 正在推动在浏览器中创建服务器的新 [FlyWeb](https://hacks.mozilla.org/2016/09/flyweb-pure-web-cross-device-interaction/) 标准。我非常兴奋，直到我意识到它与 node 中已经存在的任何东西有多么不同。

我见过的所有其他服务器都遵循一种特定的模式…

### **HTTP**

Node 已经有一个内置的简单 API，用于通过 [http](https://nodejs.org/api/http.html) 创建服务器。

```
//example -- http
//create server and define action
const server = require(“http”)
 .createServer((request, response)=>{
  ...//process request 
  response.end();//end response
});

//start server
server.listen(/*listening address*/); 
```

这可能被认为是一个标准，但是许多现代服务器应用程序都是为了处理复杂的工作流而构建的。

### **快递**

由于 http 模块非常简单， [express](https://github.com/expressjs/express) 模块是在 node 中创建服务器的事实上的标准。它将创建服务器与分配操作分开。它还使处理复杂的工作流程变得更加容易。Express 服务器有一个“使用”方法。它允许用户将多个函数链接在一起，在发送响应之前对请求进行操作。这些功能被称为“中间件”。

```
//example -- express

//create server
const server = require(“express”)();

//register middleware
server.use((request, response, next)=>{
  ...//process request
  next();//go to next middleware
});

//register middleware
server.use((request, response, next)=>{
  ...//process request
  if(request.endResponse){
    response.end();//end response
  }else{
    next();//go to next middleware
  }
});

//register middleware
server.use((request, response)=>{
  response.end();//end response
});

//start server
server.listen(/*listening address*/); 
```

此外，express 还添加了一些额外的特性，比如模板或路由，这些特性你可能需要，也可能不需要。

### **Koa 2**

Koa 是由创造 express 的同一批人创造的，被认为是它的精神继承者。这也是我选择的服务器，所以你应该[看看它是如何工作的](http://koajs.com/)，如果你还没有的话。

Koa 利用了[异步函数](https://github.com/tc39/ecmascript-asyncawait)，添加中间件的工作方式类似。

```
//example -- koa

const server = require(“koa”)();

server.use(async (context, next)=>{
  ...//process request
  next();//go to next middleware
});

server.use((context, next)=>{
  context.res.end();//end response
});

//start server
server.listen(/*listening address*/); 
```

Koa 还避免了内置的路由和模板，而是将它们作为中间件包含在外部。

请注意，Koa 2 仍在 alpha 中，但由于它的 API 比 Koa 1 的干净得多，[这里有一个教程可以帮助你直接进入](https://www.smashingmagazine.com/2016/08/getting-started-koa-2-async-functions/)，

### **小溪**

Rill 是一个[的新服务器](https://medium.com/@pierceydylan/isomorphic-javascript-it-just-has-to-work-b9da5b0c8035#.l79bgqqwx),它拥有与 KOA 2 基本相同的 API，但是开箱即可在浏览器和节点中运行。

```
//example -- rill

const server = require(“rill”)();

server.use((context, next)=>{
  context.res.end();//end response
});

server.listen(/*listening address*/); 
```

对我来说，Rill 可能有些过头了，因为它除了简单地将请求处理成响应之外，还包括了操纵 web 页面的路由特性。

### **FlyWeb**

所有其他服务器都遵循一个基本模式。

1 创建服务器

2 添加操作

3 启动服务器

虽然 rill 或 koa 可能是浏览器服务器的一个很好的起点，但 mozilla 的新 FlyWeb 服务器走了一条不同的路。

```
//example -- flyweb

//create and start server
const server = await window.navigator.publishServer(/*listening address*/);

//add actions
server.onfetch = (request)=>{
  ...//process request
  request.end();//end response
}; 
```

这打破了我们目前所见的一切模式！这里首先创建并启动服务器。只有在这之后才添加动作动作。

我们真的需要一种全新的方式来做基本相同的事情吗？

[![I know. It’s in every article. Perhaps that says something to the importance of the situation. [https://xkcd.com/license.html](https://xkcd.com/license.html)](img/12b210459cdfe71e15c9f341253ac960.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--v2SXvk0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A9nMBMt-OugnruBr_M-WuEQ.png) *我知道。每篇文章都有。也许这说明了形势的重要性。【https://xkcd.com/license.html[T6】](https://xkcd.com/license.html)*

## **@Rill/http 来救援**

rill 的创造者非常聪明。当他们创建 rill 在浏览器和 node 中工作时，他们意识到了我在本文开始时所做的事情——不同的 API 使得同构代码如此困难。因此，为了确保它在两个地方都能工作，他们创建了自己的 node 低层 [http 模块](https://nodejs.org/api/http.html)、 [@rill/http](https://github.com/rill-js/http) 的实现，它在浏览器中工作。因为它遵循相同的 API，所以只需稍加修改，它就可以与依赖于 http 的其他模块交换，并允许它们在浏览器中工作。

这正是我对 koa-2 浏览器所做的事情。在浏览器中工作的字面意思是 koa！这是一个轻量级的框架，所以它很容易与其他代码集成。因为变化相对较小，我正致力于在即将发布的 Koa 中实现这些变化，以使它成为一个开箱即用的同构应用程序。现在，你可以把你为 koa 服务器编写的几乎所有代码放在 node 中，然后转移到浏览器中。

回顾上面的服务人员示例，我们有:

```
//example -- koa-2-browser
const server = require(‘koa-2-browser’)();
server.use(/*middleware*/);
server.listen(()=>{

self.onfetch(function(event) {
    return event.respondWith(new Promise((resolve, reject)=>{
      const response = server.respondTo(event.request,{browserResponse:true});
      response.on(“finish”, (res)=>{
        resolve(res);
      });
    }));
  });

});

//Note:server.RespondTo returns a response, (here identified as "response") object that is not a suitable resolution for the promise passed to event.respond with. Passing the "browserResponse:true" option will cause the "finish" event to be resolved with a suitable instance of window.Response (identified as "res"). 
```

认识到这一点很重要，如果我们想要走向同构 JavaScript，我们应该真正避免语言本身内部的竞争标准。

这对开源软件整体来说是正确的；如果两个不同的 API 做同样的事情，它们应该有相同的结构。当我们开始创造新的东西时，我们应该关注并与已经在其他环境中做类似事情的人合作。我们花在重新发明轮子上的时间越少，我们就有越多的时间去发明新的和令人兴奋的东西(有人会玩悬浮轮子吗？).

## 你呢，缓冲区？

服务器不是跨环境的唯一竞争标准。

[* *缓冲区](https://nodejs.org/api/buffer.html) **在节点中标准化，[* *数组缓冲区](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) **在浏览器中标准化。它们都是二进制数据的容器，但工作方式略有不同。当试图创建同构应用程序时，这尤其令人讨厌。更糟糕的是，一些具有“buffer”方法的对象可能会返回 ArrayBuffer，这意味着您可能必须应用特殊的逻辑才能在不同的环境中使用它们。

经过多次修改，[* *流](https://nodejs.org/api/stream.html) **在 node 上接近完全标准化。不幸的是，[* *浏览器流](https://streams.spec.whatwg.org/) **，一个新兴的标准，引入了 API 的新的和不兼容的部分。希望相关团体能在某个时候达成一致。

还有一些其他类似的主题，比如**导入模块**，但是这种情况对她来说要复杂得多。

## 奖金库！！！

但是尝试新事物很有趣！实际上，我更喜欢 FlyWeb 创建服务器的方式。因为它[看来我不是唯一的一个](https://github.com/koajs/koa/issues/482)，我创建了另一个库， [flyweb-koa](https://github.com/johnhenry/flyweb-koa) 。它允许您以类似于 FlyWeb 的方式使用 koa，同时保持 koa 所提供的一切。

```
const koa = require(“koa-2-browser”);
//This also works with koa;

const publishServer = require(‘flyweb-koa’)(‘koa’);

const server = await publishServer(/*listening address*/);
server.use(/*middleware*/);
server.onfetch = (context)=>{
...
};
server.respondTo(/*request*/); 
```