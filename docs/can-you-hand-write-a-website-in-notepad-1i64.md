# 你能用记事本手写一个网站吗？

> 原文：<https://dev.to/ma5ly/can-you-hand-write-a-website-in-notepad-1i64>

我很喜欢 VS 代码。我的 React 应用程序像卷心菜一样生长。有了 Prettier、ESLint、IntelliSense 和 Hot Reloading 在我身边，我真的停不下来。我修复 bug，避免那些有太多让我难以忍受的糟糕和复杂代码的文件，并且欣赏一些非常简单的组件，它们使用复杂的链式数组方法和智能的动态样式在屏幕上呈现出令人难以置信的效果。

突然，我的思绪飘向了千禧年。我记得那时我也制作了[网站](https://web.archive.org/web/20021017025141/http://www.dbzworld.nl:80/nl/)。在很长一段时间里，我都是在 Microsoft Frontpage 中创建自己的网站。表格接着表格，图像接着图像，字体接着字体；我在为我所有的爱好建造小神龛。我也从妈妈给我买的一本书上学到了一点`HTML`。

所以过了一段时间，每当 Frontpage 拒绝做我想让它做的事情时，我就在记事本中打开我的`index.html`。我搜索了放置我知道可以阅读的文本的位置，以定位应该调整代码的位置。经过一点点尝试和错误，我越来越擅长于此。

我一度不再需要 Frontpage 来创建布局。我只是写纯粹的`HTML`。我想我甚至不知道`CSS`是什么。我用 Photoshop 设计我的版面，把它们切成小块，然后放入表格中。那时候我似乎过得很开心，但这让我开始怀疑...

我还能像那时那样不用在谷歌上查找任何东西，用记事本手写一个网站吗？

良好的...我试过了...答案是肯定的。但这并不好看，它伤了我的头，我想我再也不想做了。

无论如何，这是我可耻的尝试，就像我又回到了 12 岁，写一个简单的三栏网站。我发现现代浏览器对我的错误非常宽容，我很幸运在我这个年纪知道`CSS`是如何工作的。

```
<html>
  <head>
    My Handwritten Webpage
  </head>
  <body>
    <table width="1000px">
        <td bgcolor="blue" width="200px">
          <font color="white" face="tahoma"><b>Navigation</b></font>
          <ul>
            <li><a href="index.html"><font color="white">Home</font></a></li>
            <li><a href=""><font color="white">About</font></a></li>
          </ul>
          <br>
          <br>
          <br>
        </td>
        <td width="600px" bgcolor="red">
           <table>
             <tr>
               <td>
                 <marquee>
                   <font color="white" face="verdana" size="4px">My Handwritten Webpage</font>
                 </marquee>
               </td>
             </tr>
             <tr>
               <td>
                 <font face="arial" size="15px" color="pink"><i>Welcome to my page! I hope you like it :)</i>
                 </font>
                 <br>
                 <br>
               </td>
             </tr>
         <tr>
        <td bgcolor="white">
          <font>This is so very hard... I can't for the life of me remember how to do this... And I forget to refresh to see my updates.</font>
                </td>
         </tr>
           </table>
        </td>
    <td width="200px" bgcolor="orange">
          <font face="tahoma"><b>Affliates</b></font>
          <br>
          <a href="https://dev.to">Dev.to</a>
          <br>
          <br>
          <br>
          <br>
          <br>
          <br>
          <br>
        </td>
    </table>
  </body>
</html> 
```

这是一个[代码沙箱](https://codesandbox.io/s/j78qk9rqq3)中的结果

*那么，你呢？你能应付这个挑战吗？你能用纯`HTML`写一个`index.html`而不用任何谷歌搜索吗？*给我看看！