# 使用 jQuery 平滑滚动到页面部分

> 原文：<https://dev.to/attacomsian/smooth-scroll-to-page-section-with-jquery-2jng>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/smooth-scroll-page-section-jquery)。*

* * *

对于单页模板和网站，当点击锚链接时，通常会滚动到页面部分。这里有一个 jQuery 小技巧，当访问者点击导航菜单中的锚链接(或页面中的任何其他地方)时，我经常用它来平滑地滚动到页面的某一部分。将滚动速度值`1000`调整到您想要的速度。该值以毫秒为单位。

```
$(document).on('click', 'a[href^="#"]', function (e) {
    e.preventDefault();
    $('html, body').stop().animate({
        scrollTop: $($(this).attr('href')).offset().top
    }, 1000, 'linear');
}); 
```

下面是导航和部分的 HTML 标记外观:

```
<nav>
  <a href="#features">Features</a>
  <a href="#faq">FAQ</a>
  <a href="#pricing">Pricing</a>
</nav>
<!--main container-->
<div class="container">
    <!--feature section-->
    <section id="features">...</section>
    <!--faq section-->
    <section id="faq">...</section>
    <!--pricing section-->
    <section id="pricing">...</section>
</div> 
```

不想用 jQuery？你也可以使用普通的 JavaScript 来平滑滚动，但是它可能[在旧的浏览器](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) :
中不起作用

```
document.querySelectorAll('a[href^="#"]').forEach($anchor => {
    $anchor.addEventListener('click', function (e) {
        e.preventDefault();
        document.querySelector(this.getAttribute('href')).scrollIntoView({
            behavior: 'smooth',
            block: 'start' //scroll to top of the target element
        });
    });
}); 
```

也不喜欢普通的 JavaScript？这是一个纯粹的 CSS 3 解决方案，但是它只能在最新的浏览器中运行

```
body {
    scroll-behavior: smooth;
} 
```

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。