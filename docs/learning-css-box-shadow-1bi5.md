# 学习 CSS-盒子阴影

> 原文：<https://dev.to/bolah2009/learning-css-box-shadow-1bi5>

# CSS-盒子阴影

## 我想学什么，为什么

我写的是几天前我了解到的 <abbr title="Cascading Style Sheet">CSS</abbr> 阴影盒。
我之前在 freecodecamp.com 上学习 CSS 的时候发现了这个特性，并不得不在一个项目中实现它，这个项目是我单独完成的，也是我与 Microverse 的编码伙伴一起完成的。

我一直在努力理解它的作用以及价值观是如何运作的。像大多数其他 CSS 属性一样，值是不一致的(不同数量的值)。

属性的值可以从一个值到六个值，如果是嵌套的(阴影在另一个阴影上)，则可以更多

下面是属性值变化大小的例子，这种变化产生不同的效果:

```
.shadowone {
  box-shadow: 10px 10px;
}
.shadowtwo {
  box-shadow: 10px 10px 5px;
}

.shadowthree {
  box-shadow: 10px 10px 5px 5px;
}
.shadowfour {
  box-shadow: 10px 10px grey;
} 
```

[![Alt CSS Box-shadow example 1: Image with four different types of CSS box-shadow property values (associated code is above).](img/927d354b00204688b0e8620d0839097d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_f1OauGJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555220024/blog/css-b-s/1.png)

## 我学到了什么，建立了什么

我观察到的 CSS box-shadow 属性的模式如下:

*   当只给出一个值时，它通常是这些全局关键字中的一个

```
.shadow {
  box-shadow: initial;
}
.shadow {
  box-shadow: none;
}
.shadow {
  box-shadow: unset;
}
.shadowfive {
  box-shadow: 10px 10px 5px;
}
.inherit {
  box-shadow: inherit;
} 
```

[![Alt CSS Box-shadow example of when a single global value is provided in this case 'inherit' (associated code is above).](img/9fa22b5ee9a0403bab7c5d020e9816da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YhfDjNbE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555221745/blog/css-b-s/5.png)

*   这个值从它的父元素继承了框阴影属性。
*   `{box-shadow: initial;}`、`{box-shadow: none;}`和`{box-shadow: unset;}`将属性设置为默认值。

当给出两个、三个或四个长度值时，前两个将代表*水平偏移*和*垂直偏移*，接下来的两个将代表*模糊半径*和*扩散半径*。另外两个值是可选的，分别是`inset`关键字和*颜色*值。要指定多个阴影，可以提供逗号分隔的阴影列表来实现这一点。

`{box-shadow: horizontal-offset vertical-offset}`
horizontal-offset -这是一个长度值，用于设置距元素框的水平距离。

```
.shadowsix {
  box-shadow: 10px 0px;
} 
```

[![Alt CSS Box-shadow example on horizontal-offset with positive value (associated code is above).](img/f3d2069ebf2935149d75a512f99b8149.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xvRwb1Q_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555227656/blog/css-b-s/6.png)

负值会将阴影设置在元素的左侧，而正值会将阴影设置在元素的右侧。

```
.shadowseven {
  box-shadow: -10px 0px;
} 
```

[![Alt CSS Box-shadow example on horizontal-offset with negative value (associated code is above).](img/edd0cadb02d7294a2136d15fbcb9fdf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qa79QxcJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555228519/blog/css-b-s/7.png)

vertical-offset -也是一个长度值，用于设置距元素框的垂直距离。

```
.shadoweight {
  box-shadow: 0px 10px;
} 
```

[![Alt CSS Box-shadow example on vertical-offset with positive value (associated code is above).](img/0cd4a20e2d2765a0771b3fe8945c8ce2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N0wNa3Nh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555229454/blog/css-b-s/8.png)

负值将在元素上方设置阴影，而正值将在元素下方设置阴影。

```
.shadownine {
  box-shadow: 0px -10px;
} 
```

[![Alt CSS Box-shadow example on vertical-offset with negative value (associated code is above).](img/27f3607dfe466efd3ac2affc3c55ede8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qYmSW4LP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555229454/blog/css-b-s/9.png)

模糊半径-下图中的第三个值是模糊半径

```
.shadowten {
  box-shadow: 0px 0px 24px;
} 
```

[![Alt CSS Box-shadow example  (associated code is above).](img/55c9f442fb92c36cbb2788ff6a91d83d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OiuvU48J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555229927/blog/css-b-s/10.png)

这表明，即使两个偏移(前两个值)值都为零，阴影也在元素后面，仍然会产生模糊效果。这对于扩散半径也是一样的。

```
.shadoweleven {
  box-shadow: -10px -10px 24px;
}
.shadowtwelve {
  box-shadow: -10px -10px 48px;
} 
```

[![Alt CSS Box-shadow example of the spread-radius value showing the difference between a small and larger value (associated code is above).](img/6744e7e20fffc66e9edf6dd6da658b98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YLVl8GJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555230088/blog/css-b-s/11-12.png)

该值越大，模糊越大，因此阴影变得更大更亮(或更模糊)，如上图所示。

> 此值不允许负值。这将导致错误并使框阴影属性无效

扩散半径-这是第四个长度值。

```
.shadowfourteen {
  box-shadow: 0px 0px 0px 16px;
} 
```

[![Alt CSS Box-shadow example of the spread radius value  (associated code is above).](img/1cda3df6c77d46a330a3a4a770bbc0da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cPRYGOce--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555230531/blog/css-b-s/14.png)

正值将导致阴影扩大并变大，负值将导致阴影缩小，如下图所示。

```
.shadowfifteen {
  box-shadow: 10px 10px 5px 5px;
}
.shadowsixteen {
  box-shadow: 10px 10px 5px -5px;
} 
```

[![Alt CSS Box-shadow example showing two different values of the spread radius property, one positive and the other is negative (associated code is above).](img/13b01be5fcbb071e48651f785269d564.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s0CURB53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555230531/blog/css-b-s/15-16.png)

如果没有像我们之前看到的那样指定，它将是 0，阴影将和元素一样大。

`inset`inset 关键字根据如下所示的长度值，将阴影从投影更改为元素框内的阴影，在背景之上但在内容之下:

```
.shadowseventeen {
  box-shadow: 10px 10px 5px -5px inset;
} 
```

[![Alt CSS Box-shadow example of the inset keyword value  (associated code is above).](img/6582ddc849ee59f29beb3bebdaa62258.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--umZPT5dZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555230990/blog/css-b-s/17.png)

如果颜色没有像我们在前面的例子中看到的那样被指定，那么所使用的颜色将取决于浏览器——在上面的例子中通常是默认的颜色属性的值。

```
.shadoweighteen {
  color: blue;
  box-shadow: 10px 10px 5px -5px;
} 
```

[![Alt CSS Box-shadow example our the color value of an element influence the shadow color (associated code is above).](img/a74be5fe1d13bd220eec49450ec770cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vP7WxfQr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555231295/blog/css-b-s/18.png)

在这里，color 属性被设置为 blue，因此，由于没有指定颜色，所以框阴影颜色为蓝色，但是，当颜色被指定为如下所示时，框阴影颜色将更改为该值。

```
.shadownineteen {
  color: blue;
  box-shadow: 10px 10px 5px -5px green;
} 
```

[![Alt CSS Box-shadow example  (associated code is above).](img/9ed70dce9eaba242ff3941d8ec0f5ac6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ADuX1kis--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/bolaah/image/upload/v1555231295/blog/css-b-s/19.png)

## CSS 盒子的常见应用——阴影属性

CSS 框阴影主要用于导航栏和按钮。下面是我应用框阴影的项目示例:

1.  纽约时报克隆-导航栏- [在浏览器中查看](https://bolah2009.github.io/nyt-clone) - [在 Github 上查看](https://github.com/bolah2009/nyt-clone)

2.  Mint.com 的注册页面克隆-按钮-浏览器中的[视图](https://bolah2009.github.io/mint-clone)-Github 上的[视图](https://github.com/bolah2009/mint-clone)

3.  页脚链接悬停效果- [浏览器视图](https://bolah2009.github.io/tnw-clone)-[Github 视图](https://github.com/bolah2009/tnw-clone)

如果你想了解更多，这里有一些资源...

1.  [CSS 招数](https://css-tricks.com/almanac/properties/b/box-shadow/)
2.  [w3 学校](https://www.w3schools.com/cssref/css3_pr_box-shadow.asp)
3.  [MDN 网络文档](https://developer.mozilla.org/en/docs/Web/CSS/box-shadow)