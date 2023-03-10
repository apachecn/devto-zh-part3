# 如何用普通 JavaScript 构建路由器

> 原文：<https://dev.to/kodnificent/how-to-build-a-router-with-vanilla-javascript-2a18>

很多时候，我们可能希望在单个文件中编写 JavaScript 代码，但是我们希望代码只在特定路径匹配时才执行。你可以通过下载一个[路由器库](https://github.com/kodnificent/sparouter)或者自己写代码，在路由器的帮助下实现。

今天，我将带你用普通的 JavaScript 构建一个非常基本的路由器功能。我将使用一些 [es6 特性](https://www.w3schools.com/js/js_es6.asp)和 [javascript 正则表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)来构建这个路由器，所以你必须熟悉它们以便更好地理解。

## 概念

编程的好处是你可以用任何你想用的方法或风格来解决问题，但是你必须避免不好的实践。下面是我们构建这款路由器的方法。

*   创建路由器类
*   创建一个方法，将路由逻辑及其对应的回调函数存储在一个数组中。
*   创建一个处理这些逻辑的方法，如果逻辑为真，则返回相应的回调函数。

这是我们想要的照片。

```
const router = new RouterClass();

// the get() method would store the '/' logic and callback in an array;
router.get('/', function(){
   // code to be executed if '/' is matched
});

// here get() method would push '/another-page' and the callback to the existing array
router.get('/another-page', function(){
   // code to be executed if '/another-page' is matched
); 

router.init(); // this method will process the logics 
```

Enter fullscreen mode Exit fullscreen mode

## 打造我们的路由器

### 步骤 1 -创建路由器类

我们将创建一个名为 Router 的类，用关键字`new`调用它。

```
class Router {

} 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步-添加构造函数

构造函数是用 new 关键字实例化路由器类时执行的方法。在构造函数方法中，我们将创建一个名为`routes`的属性，并为其分配一个空数组。

routes 属性将所有的路由及其回调函数存储在一个数组中。

```
class Router {
    constructor(){
       this.routes = [];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以向构造函数方法传递一个`options`参数，并为 router 类设置一些选项，但为了简单起见，我们将跳过这一步。

### 步骤 3 -创建存储路线的方法

我们将创建一个名为`get()`的方法来存储路线及其回调。get 方法应该有两个参数:`uri`和`callback`T3】

```
class Router {
    constructor(){
       this.routes = [];
    }

    get(uri, callback){

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性，我将该方法命名为`get`。所以，`router.get(uri, callback);`应该是指:获取一个特定的 uri，并返回一个回调。你可以把你的名字命名为你的。也许，`router.if(uri, callback);`

### 步骤 4 -验证 get 方法的参数

在这个方法中，我们将验证我们的参数，以确保我们在使用路由器时不会错误地传递错误类型的变量作为参数。

```
class Router {

    constructor(){
       this.routes = [];
    }

    get(uri, callback){
        // ensure that the parameters are not empty
        if(!uri || !callback) throw new Error('uri or callback must be given');

        // ensure that the parameters have the correct types
        if(typeof uri !== "string") throw new TypeError('typeof uri must be a string');
        if(typeof callback !== "function") throw new TypeError('typeof callback must be a function');
        // throw an error if the route uri already exists to avoid confilicting routes
        this.routes.forEach(route=>{
            if(route.uri === uri) throw new Error(`the uri ${route.uri} already exists`);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第五步-将路线添加到路线数组中

在验证了`get()`方法的参数之后，我们将创建一个名为`route`的对象，并将该对象推送到我们现有的路由数组中。

```
class Router {

    constructor(){
       this.routes = [];
    }

    get(uri, callback){
        // ensure that the parameters are not empty
        if(!uri || !callback) throw new Error('uri or callback must be given');

        // ensure that the parameters have the correct types
        if(typeof uri !== "string") throw new TypeError('typeof uri must be a string');
        if(typeof callback !== "function") throw new TypeError('typeof callback must be a function');

        // throw an error if the route uri already exists to avoid confilicting routes
        this.routes.forEach(route=>{
            if(route.uri === uri) throw new Error(`the uri ${route.uri} already exists`);
        })

        // Step 5 - add route to the array of routes
        const route = {
            uri, // in javascript, this is the same as uri: uri, callback: callback, avoids repition
            callback
        }
        this.routes.push(route);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 6 -用`init()`方法处理路线

我们快到了！让我们使用`init()`方法处理路线。当这个方法被调用时，我们希望它遍历我们的路由数组，并将`route.uri`与`window.request.pathname`进行匹配。如果找到匹配，我们将通过返回`route.callback`函数来跳出循环。为了容易地跳出循环，我们将使用`Array.some()`方法来代替`Array.forEach()`，因为`Array.some()`将在循环中返回真值时结束循环。

```
class Router {

    constructor(){
       this.routes = [];
    }

    get(uri, callback){
        // ensure that the parameters are not empty
        if(!uri || !callback) throw new Error('uri or callback must be given');

        // ensure that the parameters have the correct types
        if(typeof uri !== "string") throw new TypeError('typeof uri must be a string');
        if(typeof callback !== "function") throw new TypeError('typeof callback must be a function');

        // throw an error if the route uri already exists to avoid confilicting routes
        this.routes.forEach(route=>{
            if(route.uri === uri) throw new Error(`the uri ${route.uri} already exists`);
        })

        // Step 5 - add route to the array of routes
        const route = {
            uri, // in javascript, this is the same as uri: uri, callback: callback, avoids repition
            callback
        }
        this.routes.push(route);
    }

    init(){
        this.routes.some(route=>{

            let regEx = new RegExp(`^${route.uri}$`); // i'll explain this conversion to regular expression below
            let path = window.location.pathname;

            if(path.match(regEx)){
                // our route logic is true, return the corresponding callback

                let req = { path } // i'll also explain this code below
                return route.callback.call(this, req);
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

很少的代码，有些奇怪的事情发生在那里，对不对？我将从正则表达式的转换开始。

我将我们的`route.uri`转换成一个正则表达式，因为我们想要匹配 route.uri 与`window.location.pathname`的精确值，否则`router.get('/about', callback)`将匹配'/about-us '，'/about-me '，因此我引入了 regExp 关键字`^`和`$`。

你也注意到了`let req = { path }`也是`let req = { path: path }`的意思。这只是为了传递一个可以通过回调参数访问的对象。实际上，这意味着:

```
const router = new Router();
router.get('/about-me', function(req){
      console.log(req.path); // outputs /about-me to the console
}
router.init(); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在构建一个基本的 javascript 路由器时，这些步骤是可以重复的。要获得更多进步，您应该瞄准以下功能:

*   具有路线参数
*   能够评估查询参数
*   有命名的路线
*   分组路线

如果你不知道如何实现这些，你可以查看我构建的路由器库的[源代码](https://github.com/kodnificent/sparouter)，看看我是如何实现这些特性的。更好的是，您可以使用`npm i @kodnificent/sparouter`通过 npm 安装这个库，并在您的脚本中使用它。查看 [npm](https://www.npmjs.com/package/@kodnificent/sparouter) 上的安装指南。

**注意**
这基本上是为了前端路由的目的。如果您想构建一个后端路由器，您可以遵循类似的过程，但是获取请求 uri 的过程将取决于服务器。

这是我在 dev.to 上的第一篇帖子，所以点击心形将非常鼓舞人心。非常欢迎评论、投稿和批评。查看我的[开发简介](https://dev.to/@kodnificent)，给我一个关注，这样我们就可以一起开发。