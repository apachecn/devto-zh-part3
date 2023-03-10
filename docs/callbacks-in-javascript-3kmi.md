# JavaScript 中的回调

> 原文：<https://dev.to/full_stackgeek/callbacks-in-javascript-3kmi>

目录

*   JavaScript 中的回调
*   [在 JavaScript 中点击 HTTP 请求](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#HittingHTTPRequests)
*   [回调抽象](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#CallbackAbstraction)

## JavaScript 中的回调:

[回调](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#Callbacks)仅仅是 JavaScript 中的[函数，在另一个函数执行完毕后被调用和执行。那么它是如何发生的呢？实际上，在 JavaScript 中，函数本身被视为对象，因此被视为所有其他对象，甚至函数也可以作为参数发送给其他函数。人们能想到的最常见的用例是 JavaScript 中的 setTimeout()函数。](https://fullstackgeek.blogspot.com/2019/01/functions-in-javascript.html) 

考虑以下 JavaScript 中的 setTimeout()示例，并尝试获得 JavaScript 默认提供给我们的实际回调。

```
//with customary function signature  
setTimeout(function() {  
  console.log('hello1');  
}, 1000);  

//with arrow function signature  
setTimeout(() => {  
  console.log('hello2');  
}, 2000);  

```

在上面的例子中，我使用了 setTimeout()函数，将一个回调函数作为参数传递给它，第二个参数是毫秒数，在此之后回调函数将被执行。我在这里展示了两种传递回调函数的方法，一种是更常用的方法，第二种是使用箭头函数的方法，这是一种更现代的方法。

## 在 Javascript 中点击 HTTP 请求:

Suppose, I want to hit an HTTP Request to an API which fetches a random text for me. We won't be digging much into details of hitting HTTP Requests as this is out of the scope of this article.
Now, to hit that API, you need to create two files:

<u>index.html</u>

```
 <!DOCTYPE html>  
 <html>  
   <head></head>  
   <body>  
     <script src="app.js"></script>  
   </body>  
 </html>  

```

```
 const puzzleAPIhit = () => {  
   const request = new XMLHttpRequest()  
   request.addEventListener('readystatechange', (e) => {  
     if (e.target.readyState === 4 && e.target.status === 200) {  
       const data = JSON.parse(e.target.responseText);  
       console.log(data.puzzle)  
     } else if (e.target.readyState === 4) {  
       console.log('An error has taken place')  
     }  
   })  
   request.open('GET', 'http://puzzle.mead.io/puzzle?wordCount=3')  
   request.send()  
 }  
 puzzleAPIhit(); 

```

现在，当您在浏览器中打开“index.html”文件时，您可以看到控制台中打印出一个随机字符串。
T3】

Suggested Read: [Redis vs MySQL Benchmarks](https://fullstackgeek.blogspot.com/2019/04/redis-vs-mysql-benchmark.html)

## 回调抽象:

Now, what if we have a complex application or something like an entire game is built over this and hence the logic of generation of a random string is something which we should keep hidden or abstract from "users". To understand this, we can create three files:
<u>index.html</u>

```
 <!DOCTYPE html>  
 <html>  
   <body>  
     <script src="makerequest.js"></script>  
     <script src="app.js"></script>  
   </body>  
 </html> 

```

<u>make request . js</u>
T5】

```
 const puzzleAPIhit = () => {  
   return 'some random string';  
 }  

```

```
 const myPuzzle = puzzleAPIhit();  
 console.log(myPuzzle);  

```

现在，我们需要用 puzzleAPIhit()函数中硬编码的 return 语句替换查找随机字符串的实际逻辑。但是由于 HTTP 请求的命中是异步的，我们不能简单地这样做:(改变 makerequest.js 的内容，保持其余两个文件不变)

<u>makerequest . js</u>

```
 const puzzleAPIhit = () => {  
   const request = new XMLHttpRequest()  
   request.addEventListener('readystatechange', (e) => {  
     if (e.target.readyState === 4 && e.target.status === 200) {  
       const data = JSON.parse(e.target.responseText);  
       console.log(data.puzzle)  
       return data.puzzle;   
       /*  
         This is absolutely impossible the request.open() is   
         asynchronous in nature.  
       */  
     } else if (e.target.readyState === 4) {  
       console.log('An error has taken place')  
     }  
   })  
   request.open('GET', 'http://puzzle.mead.io/puzzle?wordCount=3')  
   request.send()  
 }    
```

因为在控制台中，它会打印:

```
 undefined  
 Reliable Public Transportation //A random string  

```

发生这种情况是因为，由于 request.open()本质上是异步的，在 app.js 内部，情况是这样的:

*   “puzzleAPIhit()”开始执行，但由于是异步的，所以在并行运行 HTTP 请求时，它会移动到下一条语句。
*   " console . log(my puzzle)；"甚至在“puzzleAPIhit()”的执行完成之前就被执行，因此打印未定义的内容。

解？使用**回调。**下面是我们能做的:

<u>app . js</u>

```
 const myPuzzle = puzzleAPIhit((error, puzzle) => {  
   if(error) {  
     console.log(`Error: ${error}`)  
   } else {  
     console.log(puzzle)  
   }  
 });  

```

<u>make request . js</u>
T5】

```
 const puzzleAPIhit = (callback) => {  
   const request = new XMLHttpRequest()  
   request.addEventListener('readystatechange', (e) => {  
     if (e.target.readyState === 4 && e.target.status === 200) {  
       const data = JSON.parse(e.target.responseText)  
       callback(undefined, data.puzzle)  
     } else if (e.target.readyState === 4) {  
       callback('An error has taken place', undefined)  
     }  
   })  
   request.open('GET', 'http://puzzle.mead.io/puzzle?wordCount=3')  
   request.send()  
 }  

```

【index.html】<u>(没有变化)</u>

我们做了什么？我们刚刚通过发送 puzzleAPIhit()中的回调作为参数，将同步调用替换为异步调用。在 puzzleAPIhit()方法本身中，我们在得到验证回调基本定义的结果后调用回调。

T3】</u></u>