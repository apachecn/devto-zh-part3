# CSS 网格-哈努卡蜡烛图

> 原文：<https://dev.to/hey68you/css-grid---hanuka-menora-4a5n>

大家好，节日问候...

有时候你只需要一个创造性的推动来深入 CSS3 的新特性。例如，新的 CSS3 网格布局系统真的很神奇(即使你已经知道并正在使用 flexbox 布局...当然，这两个系统可以很好地协同工作)。只是想和 web 开发界分享一下我的学习成果:)

我认为 CSS Grid 最酷的一点是它简化了响应布局，在很多情况下不需要使用媒体查询。

## ***截图***

[![Screen Shot](img/5d40ccff0b5ec796663d75a17a14ea98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bFJx0FL---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ed6p3l80mu82gqrk35x9.png)

这是我的光明节密码:

*作为练习，请尝试使用网格-模板-区域(也称为“命名区域”并在此发布结果)来重新创建它*

## *T3】menora . CSST5】*

```
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

html {
    background-color: #6f6758;
}

.toprow {

}

.flame {
    height: 16px;
    width: 8px;
    background: linear-gradient(orange, red  18px);
    align-self: end;
    justify-self: center;  
    grid-row: 2/3;  
}

#flame_5_center {
    grid-column: 5/6;
    grid-row: 1/2;
}

.cup {
    height: 40px;
    width: 34px;
    background-color: rgb(230, 200, 34);    
    align-self: start;
    justify-self: center;  
    grid-row: 3/4;  
    border: black 1px solid;
    border-bottom-left-radius: 6px;
    border-bottom-right-radius: 6px;
}

#cup_5_center {
    grid-column: 5/6;
    grid-row: 2/3;
}

.sphere {
    grid-row: 4/5;
    width: 0;
    height: 0;
    border-left: 24px solid transparent;
    border-right: 24px solid transparent;
    border-top: 48px solid #929faa;
    align-self: start;
    justify-self: center;
}

#sphere_5_center {
    grid-column: 5/6;
    grid-row: 3/4;
}

.stick {
    height: 100%;
    width: 8px;
    background-color: #929faa;
    align-self: start;
    justify-self: center;  
    grid-row: 5/6;  
}

#stick_5_center {
    grid-column: 5/6;
    visibility: hidden;
}

.crossbar {
    height: 16px;
    background-color: #929faa;
    grid-row: 6/7;
    grid-column: 2/9; 
}

.left {
    height: 16px;
    background-color: #929faa;
    grid-column: 1/2;
    width: calc(50% + 4px);
    justify-self: right;
    border-bottom-left-radius: 10px;
}

.right {
    height: 16px;
    background-color: #929faa;
    grid-column: 9/10;
    width: calc(50% + 4px);
    justify-self: left;
    grid-row: 6/7;
    border-bottom-right-radius: 10px;
}

.shaft {
    grid-column: 5/6;
    width: 8px;
    background-color: #929faa;
    justify-self: center;  
    grid-row: 4/9;
}

.base {
    grid-row: 8/9;
    align-self: end;
    justify-self: center;
    grid-column: 5/6;

    width: 0;
    height: 0;
    border-left: 52px solid transparent;
    border-right: 52px solid transparent;
    border-bottom: 80px solid #929faa;
}

.grid {
    display: grid;
    padding: 20px;
    height: 100vh;
    width: 100vw;

    grid-template-columns: repeat(9, minmax(33px, 1fr));
    grid-template-rows: repeat(6, 40px) 1fr;
} 
```

还有。超文本标记语言

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="theme-color" content="#000000">
    <link rel="stylesheet" href="menora.css">
    CSS-Grid-Chanuka
  </head>
  <body>
    <div id="root" class="grid">
      <div id="top_clear_row"  class="toprow"></div>
      <div id="flame_1"        class="flame"></div>
      <div id="flame_2"        class="flame"></div>
      <div id="flame_3"        class="flame"></div>
      <div id="flame_4"        class="flame"></div>
      <div id="flame_5_center" class="flame"></div>
      <div id="flame_6"        class="flame"></div>
      <div id="flame_7"        class="flame"></div>
      <div id="flame_8"        class="flame"></div>
      <div id="flame_9"        class="flame"></div>

      <div id="cup_1"          class="cup"></div>
      <div id="cup_2"          class="cup"></div>
      <div id="cup_3"          class="cup"></div>
      <div id="cup_4"          class="cup"></div>
      <div id="cup_5_center"   class="cup"></div>
      <div id="cup_6"          class="cup"></div>
      <div id="cup_7"          class="cup"></div>
      <div id="cup_8"          class="cup"></div>
      <div id="cup_9"          class="cup"></div>

      <div id="sphere_1"         class="sphere"></div>
      <div id="sphere_2"         class="sphere"></div>
      <div id="sphere_3"         class="sphere"></div>
      <div id="sphere_4"         class="sphere"></div>
      <div id="sphere_5_center"  class="sphere"></div>
      <div id="sphere_6"         class="sphere"></div>
      <div id="sphere_7"         class="sphere"></div>
      <div id="sphere_8"         class="sphere"></div>
      <div id="sphere_9"         class="sphere"></div>

      <div id="stick_1"         class="stick"></div>
      <div id="stick_2"         class="stick"></div>
      <div id="stick_3"         class="stick"></div>
      <div id="stick_4"         class="stick"></div>
      <div id="stick_5_center"  class="stick"></div>
      <div id="stick_6"         class="stick"></div>
      <div id="stick_7"         class="stick"></div>
      <div id="stick_8"         class="stick"></div>
      <div id="stick_9"         class="stick"></div>

      <div id="crossBarL" class="left"></div>
      <div id="crossBar" class="crossbar"></div>
      <div id="crossBarR" class="right"></div>

      <div id="baseId" class="base"></div>

      <div id="shaftId" class="shaft"></div>

    </div>
  </body>
</html> 
```

如需了解更多信息，请务必查看以下链接:

*   [https://labs.jensimmons.com/](https://labs.jensimmons.com/)
*   [https://gridbyexample.com/](https://gridbyexample.com/)
*   [LayoutLand](https://www.youtube.com/channel/UC7TizprGknbDalbHplROtag)
*   [http://www.csstutorial.org/css-grid-tutorial.html](http://www.csstutorial.org/css-grid-tutorial.html)

节日快乐！**