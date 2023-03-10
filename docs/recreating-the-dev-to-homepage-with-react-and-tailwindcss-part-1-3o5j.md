# 用 React 和 TailwindCSS 重新创建 DEV.to 主页——第 1 部分

> 原文：<https://dev.to/farazpatankar/recreating-the-dev-to-homepage-with-react-and-tailwindcss-part-1-3o5j>

React 和 TailwindCSS 是这些天我构建任何东西的首选工具。React 通过它允许我们构建的组件为表格带来了可重用性，而 Tailwind 通过它的所有实用程序使构建 UI 的过程变得超级快速和简单。我想展示这些工具一起使用时的威力，我认为为我在平台上的第一篇文章重新创建 dev.to 主页是个好主意。

在第 1 部分中，我们将重新创建导航条。我认为从一些小的东西开始会很好，也使这篇文章成为对顺风的一种介绍。好吧，那我们就开始吧！

```
import React from "react";

export default function Navbar() {
  return (
    <nav className="bg-orange-100 h-12 border-b border-gray-400">
      <div className="container flex mx-auto h-full">
        <div className="flex w-full items-center mx-2">
          <div className="flex">
            
              <path d="M0 33v32h11.3c12.5 0 17.7-1.6 21.5-6.5 3.8-4.8 4.4-9 4-28-.3-16.8-.5-18.2-2.7-21.8C30.3 2.5 26.1 1 12 1H0v32zm23.1-19.1c2.3 1.9 2.4 2.3 2.4 18.5 0 15.7-.1 16.7-2.2 18.8-1.7 1.6-3.5 2.2-7 2.2l-4.8.1-.3-20.8L11 12h4.9c3.3 0 5.6.6 7.2 1.9zM46.1 3.6c-2 2.6-2.1 3.9-2.1 29.6v26.9l2.5 2.4c2.3 2.4 2.9 2.5 16 2.5H76V54.1l-10.2-.3-10.3-.3v-15l6.3-.3 6.2-.3V27H55V12h21V1H62.1c-13.9 0-14 0-16 2.6zM87 15.2c2.1 7.9 5.5 20.8 7.6 28.8 3.2 12.3 4.3 15 7 17.7 1.9 2 4.2 3.3 5.7 3.3 3.1 0 7.1-3.1 8.5-6.7 1-2.6 15.2-55.6 15.2-56.8 0-.3-2.8-.5-6.2-.3l-6.3.3-5.6 21.5c-3.5 13.6-5.8 20.8-6.2 19.5C105.9 40 96 1.9 96 1.4c0-.2-2.9-.4-6.4-.4h-6.4L87 15.2z" />
            
          </div>

          <div className="flex-1 text-center">
            <form action="/submit">
              <input
                type="text"
                placeholder="search"
                className="bg-gray-300 px-3 py-2 rounded w-4/5 lg:w-1/2 h-8"
              />
            </form>
          </div>

          <div className="flex items-center">
            <a
              href="/new"
              className="bg-teal-400 uppercase border-2 border-black py-1 px-5 font-bold mr-4 text-xs rounded hidden md:block"
            >
              write a post
            </a>
            <a href="/connect" className="h-10 w-8 mr-3">
              
                <path d="M59.84,7.897c-0.218-0.268-0.556-0.393-0.893-0.353c-0.077,0.004-0.149,0.017-0.224,0.039L0.738,23.354  C0.312,23.47,0.012,23.852,0,24.293c-0.011,0.441,0.269,0.838,0.688,0.976l21.217,6.952l-1.898,15.182  c-0.05,0.4,0.145,0.791,0.494,0.991c0.155,0.089,0.327,0.133,0.498,0.133c0.215,0,0.43-0.069,0.608-0.206l7.765-5.946l6.807,9.725  c0.188,0.269,0.494,0.427,0.819,0.427c0.022,0,0.045-0.001,0.068-0.002c0.35-0.024,0.661-0.229,0.821-0.542l22.063-43  C60.134,8.631,60.09,8.205,59.84,7.897z M52.895,11.241L22.861,30.429L4.484,24.408L52.895,11.241z M22.288,45.281l1.382-11.053  l4.555,6.507L22.288,45.281z M36.868,49.594L24.418,31.808l32.1-20.508L36.868,49.594z" />
              
            </a>
            <a href="/notifications" className="h-10 w-6 mr-3">
              
                <path d="M46.878 41.834H.956L.96 40.83c.022-4.066 2.87-7.55 6.76-8.438V20.697c0-8.93 7.265-16.197 16.196-16.197 8.932 0 16.198 7.266 16.198 16.197v11.695c3.89.89 6.737 4.372 6.76 8.437l.004 1zm-43.836-2h41.75c-.458-2.908-2.804-5.24-5.8-5.61l-.878-.107v-13.42c0-7.828-6.37-14.197-14.198-14.197S9.72 12.87 9.72 20.697v13.42l-.878.106c-2.997.37-5.342 2.702-5.8 5.61z" />
                <path d="M21.125 5.988h-2V4.792C19.125 2.15 21.275 0 23.917 0s4.79 2.15 4.79 4.792v1.176h-2V4.792c0-1.54-1.25-2.792-2.79-2.792s-2.792 1.253-2.792 2.792v1.196zm2.778 41.846c-3.94 0-7.375-2.8-8.164-6.656l1.954-.4c.6 2.93 3.21 5.057 6.205 5.057 3.06 0 5.677-2.18 6.23-5.18l1.966.36c-.725 3.952-4.17 6.82-8.195 6.82zM8.72 32.23h3.682v2H8.72zm9.447 0h20.947v2H18.167z" />
              
            </a>
            <div>
              <img
                className="h-8 w-8 rounded-full"
                src="https://res.cloudinary.com/practicaldev/image/fetch/s--0eFm7wbK--/c_fill,f_auto,fl_progressive,h_90,q_auto,w_90/https://thepracticaldev.s3.amazonaws.com/uploads/user/profile_image/165596/93b7e02d-ac3b-4961-96e2-12dbe20ce77d.jpeg"
                alt="nav-profile"
              />
            </div>
          </div>
        </div>
      </div>
    </nav>
  );
} 
```

如果你以前没有使用过 Tailwind 或基于实用程序的 CSS 库，这可能就是你现在的情况。
[![What?](img/5f2c5972bd2ea3ceb2203e2a3f7c5b1e.png)T3】](https://i.giphy.com/media/lkdH8FmImcGoylv3t3/giphy.gif)

Tailwind 网站的主页将该库描述为 ***一个高度可定制的低级 CSS 框架，它为您提供了构建定制设计*** 所需的所有构建模块，这就是它的真实面目。整个库是一堆小的实用程序类，你可以把它们放在一起，不用写一行 CSS 就能快速构建你的 UI。让我们快速看一下为了将 dev .复制到 navbar 而使用的一些实用程序。

*   我们已经使用了 [flexbox]((https://tailwindcss.com/docs/display/#flex)) 工具来控制整体布局。`flex`设置显示类型为`flex`，`items-center`是`align-items: center`的简称。
*   我们已经使用了一些[高度](https://tailwindcss.com/docs/height/)和[宽度](https://tailwindcss.com/docs/width/)实用程序来设置各种元素的属性。
*   类似地，我们使用了[边距](https://tailwindcss.com/docs/margin/)和[填充](https://tailwindcss.com/docs/padding/)工具来分隔元素。
*   我们还使用了其他几个实用程序来设置属性，从文本的字体大小和粗细，到设置容器的背景颜色，再到只在元素的一侧设置边框。同样，不用编写一行 CSS 代码！你可以在这里阅读所有这些[。](https://tailwindcss.com/)

这就是我们的处境:
[![Navbar](img/bc1469336c8cf04dadc7d81ab5fa43fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yVejtmxv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/44c05rfxfjsny3e5pgdf.png)

我要说，对于在 15 分钟左右的时间里拼凑起来的东西来说，这已经非常接近了！在下一篇文章中，我们将利用 React 的能力来构建组件，并快速整合主页的其余部分。

页（page 的缩写）这是我第一次写这样的帖子，所以我欢迎任何形式的建议。我应该多关注一些事情，少关注一些其他的事情吗？这篇文章有你喜欢的地方吗，或者有你希望它有的其他地方吗？请让我知道，这样下一个会更好。谢谢！