# 尽可能简单地解释了 async/await

> 原文：<https://dev.to/phocks/asyncawait-explained-as-simply-as-i-humanly-possibly-can-i-think-309g>

将`async`放在函数前面。

例如`async function test() {}`或`const test = async () => {}`

现在你可以在函数中使用`await`来暂停并等待值返回给我们。

这里有一个异步函数:

```
// Define our async function and let it use await
async function test() {
  const response = await fetch("https://api.github.com/"); // Wait for the Promise
  const json = await response.json(); // Wait to resolve the Promise
  console.log(json); // Log the response
}

test(); // Run the function 
```

Enter fullscreen mode Exit fullscreen mode

好了，就这样！现在明白了吗？

如果没有，[请点击此处](https://dev.to/siwalik/async-programming-basics-every-js-developer-should-know-in-2018-a9c)获得更好的解释。或者留下评论，我会尽力帮忙的。