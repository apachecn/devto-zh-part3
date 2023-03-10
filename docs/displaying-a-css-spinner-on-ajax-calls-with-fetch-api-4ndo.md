# 用 fetch api 在 ajax 调用上显示 css 微调器

> 原文：<https://dev.to/wangonya/displaying-a-css-spinner-on-ajax-calls-with-fetch-api-4ndo>

每当我使用`fetch`时，我总是不得不寻找如何做到这一点，或者查阅我以前的代码。有一阵子，我用了解决[这个](https://stackoverflow.com/questions/43792026/display-spinner-during-ajax-call-when-using-fetch-api) SO 问题的办法。这是一个正确的解决方案，效果很好，但老实说，如果有人让我解释我的那段代码，我真的无法很好地解释*发生了什么。所以我想到了一个简单的方法。真的很简单，我想我只是想多了。方法如下:*

 *## 设置 HTML

```
<!-- this will show our spinner -->
<div id="spinner"></div> 

<!-- And this will fetch our data -->
<button onclick="fetchData()">Load data</button> 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 CSS 微调器

```
#spinner{
  visibility: hidden;
  width: 80px;
  height: 80px;

  border: 2px solid #f3f3f3;
  border-top:3px solid #f25a41;
  border-radius: 100%;

  position: absolute;
  top:0;
  bottom:0;
  left:0;
  right: 0;
  margin: auto;

  animation: spin 1s infinite linear;
}

@keyframes spin {
  from {
      transform: rotate(0deg);
  } to {
      transform: rotate(360deg);
  }
}

#spinner.show {
  visibility: visible;
} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，微调器可见性是隐藏的。这是我们将操作的元素，以便根据需要显示和隐藏它。

## 用 Javascript 做事

我将使用[随机用户生成器 api](https://randomuser.me/) 。

```
const spinner = document.getElementById("spinner");

function showSpinner() {
  spinner.className = "show";
  setTimeout(() => {
    spinner.className = spinner.className.replace("show", "");
  }, 5000);
}

// function hideSpinner() {
//   spinner.className = spinner.className.replace("show", "");
// }

function loadData() {
  showSpinner()
  fetch('https://randomuser.me/api/')
  .then(response => response.json())
    .then(data => {
    // hideSpinner()
    console.log(data)
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

当`showSpinner()`被调用时，它将一个`show`类添加到`spinner`元素中，这将按照我们在 css 中指定的方式打开`visibility: visible`。为了隐藏微调器，移除了`show`类，从而转动了`visibility: hidden`。

出于演示的目的，我使用了一个超时函数，并将其设置为 5 秒，这样 spinner 就可以加载，因为获取数据需要 1 秒钟。但理想情况下，你会希望有一个`hideSpinner()`函数，并在数据返回后调用它。然后，超时时间应该增加到 15 秒，因为你不希望它永远加载，之后你会显示一个错误。

这是笔*