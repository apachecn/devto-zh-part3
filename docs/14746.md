# 用 HTML CSS 和 JS 构建评分应用

> 原文：<https://dev.to/straleb/building-a-rating-app-with-html-css-and-js-2ff>

最近，我一直在做一个评级应用程序，在开始时，我有点挣扎，幸运的是开发社区很大，所以我收集了一些解决方案并完成了我的项目。我决定构建一个类似的简单示例，并分享我的构建方法。对于我的模型，我使用了一些链接到 [Google Play](https://play.google.com/store) 的应用程序，你可以随时查看。

首先，我们需要从 cdnjs Cloudflare 导入 awesome 字体，稍后我们将把它用于星形图标

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css"> 
```

为了设计导航条和其中的内容，包括容器中的应用程序列表，下面的 HTML 代码和 CSS 代码应该是这样的

```
<nav>
        <ul>
            <li><a href="#">Home</a></li>
            <li><a href="#">Apps</a></li>
            <li><a href="#">Downloads</a></li>
        </ul>
    </nav>
</div>
<!--App list rating design-->
<div class="app-list">
<ul>
        <li>
            <img src="https://i.ibb.co/q1Lcz6b/badlandbrowl.png" alt="appIcon" height="60" width="60">
            <a href="https://play.google.com/store/apps/details?id=com.frogmind.badlandbrawl&hl=en">Badland Browl</a>
            <figcaption><i>Sling your Clones onto the battlefield! Master your timing and...</i></figcaption>
          <div class="rating">
            <span style=" color: #FDE16D;"  class="fa fa-star"></span>
            <span style=" color: #FDE16D;" class="fa fa-star "></span>
            <span style=" color: #FDE16D;" class="fa fa-star "></span>
            <span style=" color: #FDE16D;" class="fa fa-star"></span>
            <span style=" color: #FDE16D;" class="fa fa-star"></span>
        </div> 
```

```
/*Container of apps and menus*/
.container{
    background: rgb(87, 87, 87);
    width: 80em;
    height: 40em;
    border: 1px solid black;
    margin: 5em auto;
}
 /*Navigation bar and search bar*/
.navbar{
    height: 3.5em;
    background: rgb(59, 59, 59);
}

input[placeholder="search app"]{
    float: right;
    outline: none;
    margin: 1em 1.5em;
    height: 1.5em;
}

a img[alt=menuIcon]{
    margin:0.4em 1em;
}

nav a{
    text-decoration: none;
    font-size: 25px;
    font-family: 'Lato', sans-serif;
    color: rgb(2, 218, 218);
} 
```

并添加列表中的其他游戏，下面的代码应该是这样的:

[![alt text](img/daead9d91ab8c2835a23a88a09677c9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xw-6Imyh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/bJ42KCg/Screenshot-2.png)

为了自定义动画，并从一颗星从左向右移动获得评级效果，而不跳过从一颗星到另一颗星的颜色，我们将在下面的 CSS 代码中的星评级代码中添加“内容:' \2605 '”

```
 .rating > .fa:hover,
  .rating > .fa:hover  ~ .fa{
    text-shadow: 0 0 9px rgba(238, 255, 0, 0.788);
    color: #FDE16D;
    content: '\2605'; /* Full star in UTF-8 */
    position: relative;
    left: 0;
    }

    .fa.rated::before{
        /* the :hover should come first */
        content: '\2605';
        color: #FDE16D;
        font-size: 40px;
      } 
```

通过下面的代码，我们将得到这样的效果

[![alt text](img/74755e8c83455e49d5bbfbec048aa345.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ASZf-8vL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/VNZMPXz/Screenshot-3-side.png)

有了实现的效果，剩下要做的一件事就是放一个代码，它会离开评级，不会让它在鼠标离开星形范围后消失。我们将使用一个带有“ **for loop** 的代码来确定星星的界限，并使用“ **if else** 语句来说明当我们将鼠标悬停在一颗或多颗星星上时，我们单击的颜色保持不变，并将其更改为黄色，当我们将其降级时，它将恢复为原来的白色。
代码将在最后一个游戏中实现，你将能够在我放在底部的笔评级。

```
var count;

function starmark(item){
count=item.id[0];
sessionStorage.starRating = count;
var subid= item.id.substring(1);
for(var i=0;i<5;i++)
{
if(i<count){
document.getElementById((i+1)+subid).style.color="#FDE16D";
}
else{
document.getElementById((i+1)+subid).style.color="white";
  }
 } 
} 
```

完成所有工作后，最终产品应该是这样的

[https://codepen.io/SBabic/embed/vbLgvW?height=600&default-tab=result&embed-version=2](https://codepen.io/SBabic/embed/vbLgvW?height=600&default-tab=result&embed-version=2)

感谢您花时间阅读我的文章，我真诚地希望您能够学到一些东西，并喜欢阅读它。🙂

如果你想了解更多关于星级应用程序以及如何制作它们，这里有一些类似的文章和项目，你可以看看。

[WebSchools 简单星级评定](https://www.w3schools.com/howto/howto_css_star_rating.asp)
[简单星级评定 Javascript 技术](https://webdesign.tutsplus.com/tutorials/a-simple-javascript-technique-for-filling-star-ratings--cms-29450)
[用 Vanila JS 创建基于星级评定的应用](https://gomakethings.com/creating-a-star-based-rating-app-with-vanilla-javascript/)