# 我个人网站的第一次设计迭代

> 原文：<https://dev.to/mazentouati/a-first-design-iteration-of-my-personal-website-37p6>

嗨，伙计们

最近，我在网上分享了我的第一个个人网站，并寻求反馈和建议。幸运的是，我已经得到了足够的东西来进行另一次设计迭代。

在我开始谈论我所做的改变之前，我邀请你先来看看我的网站，以防你错过了或者来看看新的版本。

[马赞图阿蒂-旧版本](https://sunchayn.github.io/old)

。
。
。
。
。
。
。
。

非常欢迎回来，

首先，我真的很高兴第一个版本在不到一周的时间里获得了一些关注，并影响到了世界各地的许多人。

[![](img/d12cb670a4f3c87d4c9d5e2de79ff888.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EOiwBqwi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0zb1j5kdbb4rdnq07luq.PNG)

感谢开发者社区分享爱

## 修改日志

#### 通用

*   我改变了字体系列和大小(我把它们变得更大，更易读)
*   我改写了文本内容
*   仅适用于移动用户:现在导航栏的所有部分都是蓝色背景
*   我减少了暴露的延迟

#### 你好世界章节

*   我让绿色文字发光
*   我加了一个图标表示键盘体验(后面会讲到)

#### 关于节

*   我删除了版本切换器，现在只有完整版是可访问的。原因是:我不想让访问者错过关于我的重要细节，因为他很可能会选择捷径，如果他足够感兴趣，我宁愿他阅读完整版本，以获得更好的视野。
    [![](img/a6cd7bc974fcfdb04bac9dc6c6f3b80b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--XEvOV5Tz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/51mrhlva1ozb1jh3qjgf.jpg)

*   我制作了签名自画(为不支持 SVG 嵌入的浏览器显示一个静态图像)

[![](img/3786fc94e1ad72b3a4d0789f10c80f56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9jfQxFyW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/81z8xt6rbuhzmau54tf6.gif)

#### 项目部

*   在最初的版本中，我犯了一个愚蠢的 UI 错误，直到我发布网站时才注意到。我让项目看起来像两个独立的组，没有在元素之间保持水平和垂直的间距。现在修好了。

[![](img/d8b180f7002aacdb035eeead87baf96e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--msXSSdYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3zvj2uuvvsl0bblxez2r.jpg)

*   我改变了悬停状态的动画。

[![](img/814dea524b476fcab2c4f6978bfcc70d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wmOsDD8j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5pwz8c4jsweshs7q3krs.gif)

#### 博客板块

*   我为这个部分添加了一个标题和描述
*   我添加了一个按钮来查看所有文章

#### 新增部分:联系人部分

*   我从 BSOD 部分提取了联系人部分![](img/7d86f67d0e08f039f0118faf2ec5577d.png)

#### BSOD 节

*   我添加了一个完成状态动画，让它看起来更恐怖
*   我把颜色改得不那么亮了

#### 新功能:键盘体验

以前我让链接看起来像命令行应用程序。它在括号中显示了动作的字母，例如[G]ithub，但由于技术原因，我没有让它起作用。问题是，即使在同一个视口中，也有多个链接具有相同的名称。我想了想，想出了一个解决方案，就是触发活动块中的链接。基于鼠标位置选择活动块

*这是一个测试功能，如果你发现任何错误行为，请告诉我*

#### 第 404 页

我注意到以前的流量没有达到 404 页面，这是该页面的屏幕截图

[![](img/5519b8d1f81b13fb27614cce0a789102.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s5xu5iR6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a7xlquhkjfsu3mmfbna8.png)

404 页的一个活生生的例子:[http://sunchayn.github.io/somethingNotFound](http://sunchayn.github.io/somethingNotFound)

#### 杂项

*   我做了与搜索引擎优化相关的页面结构的变化
*   我优化了 CSS 的内容交付(在 HTML 中注入了关键的 CSS)

## 未来

*   我计划使用 PWA 技术使网站离线
*   更加注重优化内容交付和搜索引擎优化

## 感谢

最后，我想感谢那些在不同社区给我反馈和建议的慷慨的人们:Dev 社区，Twitter，Reddit。

特别是这些人:

[![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel)

## [阿里](/aspittel)小喷壶<button name="button" type="button" data-info="{&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:38627,&quot;name&quot;:&quot;Ali Spittel&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow user: Ali Spittel" aria-pressed="false">跟随</button>

[Passionate about education, Python, JavaScript, and code art.](/aspittel)
[![nektro image](img/cb4b59d5a2101f651752b1c1936faa81.png)](/nektro)

## [梅根(她/她)](/nektro) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:10538,&quot;name&quot;:&quot;Meghan (she/her)&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow user: Meghan (she/her)" aria-pressed="false">跟随</button>

[24\. Local trans witch who prefers to do magic with a keyboard. she/her. Currently hacking away at making the Web less centralized.](/nektro)
[![julesmanson image](img/22d2853fe9b3e6cf6ffefb76bdf93720.png)](/julesmanson)

## [朱尔斯·曼森](/julesmanson) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:99032,&quot;name&quot;:&quot;jules manson&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow user: jules manson" aria-pressed="false">跟随</button>

[Mechanical engineer who dabbles with Vanilla Javscript and web development as a hobbby. I don't use any libraries or frameworks as they take the fun out creating your own solutions.](/julesmanson)
[![qm3ster image](img/c16b359de74e48cd9b0f9aedd8abd96c.png)](/qm3ster)

## [米哈伊尔·马洛](/qm3ster) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:83883,&quot;name&quot;:&quot;Mihail Malo&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow user: Mihail Malo" aria-pressed="false">跟随</button>

特别感谢 Tyler Charboneau 为我的整个网站校对并建议了更好的文本内容。

我总是很高兴听到更好的想法和建议。请让我知道如果你有一些，和平。