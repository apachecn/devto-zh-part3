# 用普通 Javascript 延迟加载图像

> 原文：<https://dev.to/guimg/lazy-load-images-with-vanilla-javascript-58gd>

## 什么是懒装？

当然，你们中的许多人都已经听说过，谷歌 Chrome 将以一种带有 HTML 标签的本地方式为图像和 iframes 添加 Lazy Loading。

惰性加载的基本意思是直到用户的视野中没有这些图像时才加载它们的内容。这样，如果用户没有到达网页末端，位于该处的图像将永远不会被加载。有了这个解决方案，我们节省了加载时间，提高了速度。

在本教程中，我们将延迟加载 30 张图片，这些图片之前是从 API 中获取的。首先，我们将编写 HTML，然后是 JS 代码，最后我们将实现一些基本的 CSS 来获得一个漂亮的结果。这里有一个演示:

[https://codepen.io/GuimG/embed/wbByyY?height=600&default-tab=result&embed-version=2](https://codepen.io/GuimG/embed/wbByyY?height=600&default-tab=result&embed-version=2)

## 我们的 HTML 骨架

这是我们的`index.html`文件。它只有一个标题、一个加载微调器和一个空的`div`,我们将把来自 API 的所有图像放在这里。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    Lazy Loading Images

    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <h1>Lazy Loading Images</h1>

    <!-- Images Container -->
    <div id="imagesContainer"></div>

    <!-- Loading spinner -->
    <div class="lds-roller" id="lds-roller">
      <div></div>
      <div></div>
      <div></div>
      <div></div>
      <div></div>
      <div></div>
      <div></div>
      <div></div>
    </div>

    <!-- Our custom JS code -->
    <script src="main.js"></script>
  </body>
</html> 
```

此时，我们的页面是空的。让我们创建我们的`main.js`文件，抓取一些图像并缓慢加载它们。

## 这里就是神奇发生的地方

因为这个文件会更复杂，所以让我们把代码分成更小的部分，这样我们就可以深入理解每一部分。下面所有的代码都写在了`main.js`文件中。

```
window.onload = () => {
  // Get 30 random images
  fetch('https://picsum.photos/v2/list?limit=30')
    .then(function(response) {
      return response.json();
    })
    .then(function(myJson) {
      // Call the createImages function to generate the HTML code
      createImages(myJson);
      // Remove the loading spinner
      document.getElementById('lds-roller').remove();
    })
    .catch(err => {
      console.log(err);
    });
}; 
```

第一个功能将在窗口准备就绪时触发。我们使用`fetch` API 从外部 API 获取图像。这个 API 用一个包含所有图片 url 的 JSON 文件来响应我们。有了这个 url，我们现在可以创建我们的 HTML 代码了。

```
function createImages(imgs) {
  for (let i of imgs) {
    // Create an image HTML tag
    const image = document.createElement('img');
    image.setAttribute('data-lazy', i.download_url);
    image.classList.add('lazy-loading');
    document.getElementById('imagesContainer').appendChild(image);
  }
  // Sets an observer for each image
  lazyTargets = document.querySelectorAll('.lazy-loading');
  lazyTargets.forEach(lazyLoad);
} 
```

注意，我们没有设置图像的`src`属性。我们正在创建一个`data-lazy`版本。这样，图像在创建时不会请求任何数据。现在让我们来实现延迟加载功能。这个函数将使用`intersection observer` API 来知道每个图像何时出现在视图中。然后它会用`data-lazy`属性替换`src`属性。

```
function lazyLoad(target) {
  const obs = new IntersectionObserver((entries, observer) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const img = entry.target;
        const src = img.getAttribute('data-lazy');

        img.setAttribute('src', src);
        img.classList.add('fadeIn');

        observer.disconnect();
      }
    });
  });
  obs.observe(target);
} 
```

## 让我们把它变漂亮

现在我放了一些基本的 CSS 来让事情看起来更漂亮。HTML 中引用的文件是`style.css`。

```
html,
body {
  margin: 0;
  padding: 0;
  width: 100%;
}

h1 {
  text-align: center;
  font-family: sans-serif;
  font-weight: 200;
}

#imagesContainer {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  margin: auto;
  width: 680px;
}

#imagesContainer img {
  width: 300px;
  min-height: 200px;
  margin: 20px;
  box-shadow: 5px 10px 15px;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

.fadeIn {
  animation-name: fadeIn;
  animation-duration: 3s;
} 
```

原来如此！下面是 [CodePen](https://codepen.io/GuimG/full/wbByyY) 的链接。回头见！