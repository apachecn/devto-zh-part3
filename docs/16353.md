# 使用 IntersectionObserver API 进行延迟加载

> 原文：<https://dev.to/itachiuchiha/lazy-loading-with-the-intersectionobserver-api-44pe>

你好。在这篇文章中，我将给出一些关于[intersect observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)的信息。

在我们开始之前，我应该说这个 IE11 不支持这个 API。您可以使用查看[上支持哪些浏览器。](https://caniuse.com/#search=IntersectionObserver)

## 为什么？

有很多库可以帮助延迟加载操作。我知道，但是这个 API 是本地的。你可能不想使用外部库等。

在一个项目中，我们有许多图像和许多列表项。因为这些图片，页面加载只需要 10-15 秒。这对我们和我们的顾客、游客等来说是可怕的。

我们找到了一些解决方案，比如服务器端分页。但是这对于我们的项目来说不是一个好的解决方案。因为客户不想重新加载页面。

为了解决问题，我们找到了 Verlok 的 [lazyload](https://github.com/verlok/lazyload) 库。但是我们也发现了**intersect observer**API。无论如何，我们决定使用 IntersectionObserver API。我们今天使用 IntersectionObserver。好吧，我来示范一下。

## HTML & & CSS 代码

在本例中，我们将使用 Bootstrap 4 的卡组件。我们还使用按钮组件来跟踪索引。默认索引将是 0(零)。

```
<div class="container">
    <div class="row">
        <div class="col-md-12 card-container">

        </div>
    </div>
    <div class="row">
        <div class="col-md-12 load-more-container">
            <button data-index="0" class="btn btn-primary btn-block load-more">Load More</button>
        </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

CSS 会是这样的。就差一点点。我们的卡片现在看起来更漂亮了。

```
.load-more-container {
    margin-top: 15px;
}

.card {
    margin-bottom: 15px;
} 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 代码

我将创建一个类。您可能想要创建一个函数。这个看你了。课程会这样开始。它的名字将是 **LazyLoad** 。

```
class LazyLoad {
    constructor() {
    this.btn = document.querySelector(".load-more")
    this.cardContainer = document.querySelector('.card-container')
    this.BASE_URL = 'https://picsum.photos/list'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将为图像提供 API 服务器。

在这个例子中，我们总是从这个服务器调用所有的图像。因为我找不到任何页码。

然后，我将创建一个名为 **load** 的函数。

```
load() {
        fetch(this.BASE_URL)
        .then(resp => resp.json())
        .then(obj => {
            let start = (this.btn.dataset.index == 0) ? parseInt(this.btn.dataset.index) : parseInt(this.btn.dataset.index) + 1
            let end = start + 5
            let arr = obj.slice(start, end)
            if(arr.length > 0) {
                let cards = '';
                arr.forEach(f => {
                    cards += `
                        <div class="card">
                          <div class="card-body">
                            <h5 class="card-title">${f.author}</h5>
                            <img src="https://picsum.photos/458/354/?image=${f.id}" alt="">
                          </div>
                        </div>
                    `
                })

                this.cardContainer.insertAdjacentHTML('beforeend',cards)
                this.btn.dataset.index = end
            }
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

通过这个函数，我们将创建获取请求。我们还将借助模板文字创建 HTML 内容，并将它们放入`.card-container`。

最后，我们将创建一个名为 **init** 的函数。功能会是这样的；

```
 init() {
        const container = document.querySelector(".load-more-container")
        let observer = new IntersectionObserver(entries => {
            entries.forEach(entry => {
                const { isIntersecting } = entry

                if(isIntersecting) {
                    this.load()
                    //observer = observer.disconnect()
                }
            }, {
                root: container
            })
        })

        observer.observe(container)
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，当元素与用户的 viewport 代码相交时就会运行。IntersectionObserver 给出了两个参数。第一个参数是回调。第二个理由是期权。在本例中，我们只有`root`个选项。

接收一个或多个交叉点条目的回调。观察者将会观察我们创建的容器。

```
observer.observe(container) 
```

Enter fullscreen mode Exit fullscreen mode

使用`observer = observer.disconnect()`您将停止观察操作。这意味着，更多的负载功能将不起作用。

## 整理前

如果你想要一个真实的例子，我已经在 CodePen 上创建了一个例子。你可以检查它是如何工作的。

[https://codepen.io/aligoren/embed/wRYpQy?height=600&default-tab=result&embed-version=2](https://codepen.io/aligoren/embed/wRYpQy?height=600&default-tab=result&embed-version=2)

## 资源

[MDN](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver/IntersectionObserver)

大卫·沃尔什

我希望这篇文章对你的项目有所帮助。

**感谢阅读！**