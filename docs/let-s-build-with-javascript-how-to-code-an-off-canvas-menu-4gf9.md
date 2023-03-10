# 让我们构建:用 JavaScript——如何编写一个非画布菜单

> 原文：<https://dev.to/justalever/let-s-build-with-javascript-how-to-code-an-off-canvas-menu-4gf9>

我又回到了我的“让我们来构建”系列的另一部分:JavaScript 系列。本教程基于画布外的菜单体验，它是使用`HTML`、`CSS`和`JavaScript`的组合构建的。

你可以单独用 CSS 完成很多事情，本教程就是一个很好的例子。我们将使用少量的 JavaScript 来简单地在侦听点击事件时切换类。最终的结果是一个类似应用程序的用户界面，它使用一个汉堡菜单来切换主导航的打开和关闭。当切换导航时，我用 CSS 添加了过渡属性，以在一切发生时给出一个动画外观。

[查看密码本](https://codepen.io/webcrunchblog/pen/jXOwRg)

[https://www.youtube.com/embed/yv-0_yA_TH8](https://www.youtube.com/embed/yv-0_yA_TH8)

### 标记

该标记使用三个主要组件，其中一个是隐藏的。当用 JavaScript 添加类时，另外两个会移动。

```
<section class="top-banner">
  <div class="top-banner-overlay">
    <h1>Hello World</h1>
    <p>Web-Crunch is a blog about bringing the latest trending news and tutorials for all designers and developers alike. Many other blogs focus on a single niche. Web-Crunch is a small team of writers, designers, and developers who are committed to authoring the best of the best when it comes to the world of design and development. We crunch our ideas and topics together to deliver high quality content for your viewing pleasure.</p>
  </div>
</section>

<nav class="top-nav">
  <div class="menu-wrapper">
    <ul class="menu">
      <li>
        <a href="#">Home</a>
      </li>
      <li>
        <a href="#">About</a>
        <ul class="sub-menu">
          <li>
            <a href="#">Company</a>
          </li>
          <li>
            <a href="#">People</a>
          </li>
        </ul>
      </li>
      <li>
        <a href="#">Projects</a>
      </li>
      <li>
        <a href="#">Clients</a>
      </li>
      <li>
        <a href="#">Contact</a>
      </li>
    </ul>
    <button class="menu-close" aria-label="close menu">x</button>
  </div>

  <div class="fixed-menu">
    <button class="menu-open" aria-label="open menu">
      <div class="menu-icon">menu<path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z"></path></div>
    <div class="menu-text screen-reader-text">Menu</div>
    </button>

    <ul class="social-links">
      <li>
        <a href="https://facebook.com/webcrunchblog">Facebook icon<path d="M22.676 0H1.324C.593 0 0 .593 0 1.324v21.352C0 23.408.593 24 1.324 24h11.494v-9.294H9.689v-3.621h3.129V8.41c0-3.099 1.894-4.785 4.659-4.785 1.325 0 2.464.097 2.796.141v3.24h-1.921c-1.5 0-1.792.721-1.792 1.771v2.311h3.584l-.465 3.63H16.56V24h6.115c.733 0 1.325-.592 1.325-1.324V1.324C24 .593 23.408 0 22.676 0"></path></a>
      </li>
      <li>
        <a href="https://twitter.com/webcrunchblog">Twitter icon<path d="M23.954 4.569a10 10 0 0 1-2.825.775 4.958 4.958 0 0 0 2.163-2.723c-.951.555-2.005.959-3.127 1.184a4.92 4.92 0 0 0-8.384 4.482C7.691 8.094 4.066 6.13 1.64 3.161a4.822 4.822 0 0 0-.666 2.475c0 1.71.87 3.213 2.188 4.096a4.904 4.904 0 0 1-2.228-.616v.061a4.923 4.923 0 0 0 3.946 4.827 4.996 4.996 0 0 1-2.212.085 4.937 4.937 0 0 0 4.604 3.417 9.868 9.868 0 0 1-6.102 2.105c-.39 0-.779-.023-1.17-.067a13.995 13.995 0 0 0 7.557 2.209c9.054 0 13.999-7.496 13.999-13.986 0-.209 0-.42-.015-.63a9.936 9.936 0 0 0 2.46-2.548l-.047-.02z"></path></a>
      </li>
      <li>
        <a href="https://youtube.com/c/webcrunch">YouTube icon<path class="a" d="M23.495 6.205a3.007 3.007 0 0 0-2.088-2.088c-1.87-.501-9.396-.501-9.396-.501s-7.507-.01-9.396.501A3.007 3.007 0 0 0 .527 6.205a31.247 31.247 0 0 0-.522 5.805 31.247 31.247 0 0 0 .522 5.783 3.007 3.007 0 0 0 2.088 2.088c1.868.502 9.396.502 9.396.502s7.506 0 9.396-.502a3.007 3.007 0 0 0 2.088-2.088 31.247 31.247 0 0 0 .5-5.783 31.247 31.247 0 0 0-.5-5.805zM9.609 15.601V8.408l6.264 3.602z"></path></a>
      </li>
    </ul>
  </div>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码仅仅是这个站点的占位符内容，以及一些 svg 图标，用于将社交图标呈现为每个社交媒体巨头的链接。

### CSS/SCSS

我们的 CSS 为我们的体验做了很多繁重的工作。应用了多个转换和变换属性，使得 JavaScript 中的类切换更加生动。

```
* { 
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

ul {
  list-style: none;
}

button {
  border: none;
  background: transparent;
  outline: none;
  cursor: pointer;
}

button:active {
  color: black;
}

a {
  text-decoration: none;
  color: black;
}

body {
  font: normal 16px/1.5 Helvetica, sans-serif;
}

.screen-reader-text {
  postion: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  -webkit-clip-path: inset(50%);
  clip-path: inset(50%);
  border: 0;
}

.top-banner {
  display: flex;
  width: calc(100% - 60px);
  height: 100vh;
  transform: translateX(60px);
  background: url(https://images.unsplash.com/photo-1517433247151-80815904f4b6?ixlib=rb-1.2.1&q=85&fm=jpg&crop=entropy&cs=srgb&ixid=eyJhcHBfaWQiOjE0NTg5fQ) no-repeat center / cover;
}

.top-banner-overlay {
  display: flex;
  flex-direction: column;
  justify-content: center;
  width: 350px;
  padding: 20px;
  transition: transform 0.4s;
  color: white;
  background: rgba(0, 0, 0, 0.7);

  &.is-moved {
    transform: translateX(350px);

    &:before {
      content: "";
      position: absolute;
      top: 0;
      bottom: 0;
      right: 100%;
      width: 20px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.9);
    }
  }

  p {
    font-size: 1rem;
    margin-top: 10px;
  }
}

.top-nav {
  li + li {
    margin-top: 8px;
  }
  .menu-wrapper {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    width: 60px;
    padding: 20px;
    transform: translateX(-350px);
    transition: tranform 0.4s;
    background: #A0F0ED;

    &.is-opened {
      transform: translateX(60px);
      width: 350px;

      .menu {
        opacity: 1;
        transition-delay: 0.4s;
      }

      a { font-size: 1.2rem; }
    }
  }

  .menu {
    opacity: 0;
    transition: opacity 0.3s;
  }

  .sub-menu {
    padding: 10px 0 0 8px;
  }

  .menu-close {
    position: absolute;
    top: 0;
    right: 20px;
    font-size: 1.6rem;
  }

  .fixed-menu {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    display: flex;
    flex-direction: column;
    width: 60px;
    padding: 20px;
    color: #20504F;
    background: #64D5CA;

    .menu-open {
      display: inline-flex;
      align-items: center;
      text-align: left;
      margin: 0 0 auto;
      width: auto;
      cursor: pointer;
      color: #20504F;

      svg { fill: #20504F; }

      &:hover, &:focus {
        opacity: 0.9;
      }
    }
    .menu-icon { margin-right: 10px; }
  }
}

.social-links svg {
  fill: #20504F;
} 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript

我们的 JavaScript 太简单了。我定义了一个包含两个事件监听器的函数。每个监听器监听一个 click 事件，该事件切换显示和隐藏主导航所需的几个类。

```
const menuOpen = document.querySelector('.top-nav .menu-open');
const menuClose = document.querySelector('.top-nav .menu-close');
const menuWrapper = document.querySelector('.top-nav .menu-wrapper');
const topBannerOverlay = document.querySelector('.top-banner-overlay');

function toggleMenu() {
  menuOpen.addEventListener('click', () => {
    menuWrapper.classList.add('is-opened');
    topBannerOverlay.classList.add('is-moved');
  });

  menuClose.addEventListener('click', () => {
    menuWrapper.classList.remove('is-opened');
    topBannerOverlay.classList.remove('is-moved');
  });
}

toggleMenu(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 探索系列到此为止

*   [让我们构建:使用 JavaScript–DIY 下拉菜单和响应菜单](https://web-crunch.com/lets-build-with-javascript-diy-dropdowns-responsive-menus/)
*   [让我们用 JavaScript 构建——带 Cookies 的广播栏](https://web-crunch.com/lets-build-with-javascript-broadcast-bar-cookies/)
*   [让我们用 JavaScript 构建:粘性导航](https://web-crunch.com/lets-build-with-javascript-sticky-nav/)
*   [让我们构建:用 JavaScript 动态标签](https://web-crunch.com/how-to-create-tabs-with-vanilla-javascript)
*   [让我们构建:用 JavaScript–Modals](https://web-crunch.com/lets-build-with-javascript-how-to-code-modal-vanilla-javascript/)
*   [让我们构建:使用 JavaScript–html 5 视频播放器](https://web-crunch.com/lets-build-with-javascript-html5-video-player/)
*   [让我们构建:用 JavaScript——手风琴](https://web-crunch.com/how-to-create-accordion-vanilla-javascript/)
*   [让我们构建:用 JavaScript 骨架屏幕效果](https://dev.tohow-to-create-skeleton-screen-loading-effect)

帖子[如何编写一个非画布菜单——让我们用 JavaScript](https://web-crunch.com/off-canvas-menu/) 首先出现在[网站](https://web-crunch.com)上。