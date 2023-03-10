# 面向 CSS 作者的 Firefox DevTools

> 原文：<https://dev.to/bnevilleoneill/firefox-devtools-for-css-authors-55om>

[![](img/06fc5e7ff079bec312dc10a95441315d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wQ8ngQJI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACH1pylIf1cFQsdozu0Kfmw.jpeg)

如果你是一名开发人员，很有可能有一套你非常喜欢的工具，当然，任何浏览器开发工具都是清单上的第一号。

随着 Firefox 快速发布对其 DevTools 的惊人改进，这是研究专门针对 CSS 作者的强大方面的好时机。

在本文中，我将探索这些特性，以帮助您在浏览器中创作和检查 CSS。我确信我将要分享的将会让你对 Firefox DevTools 的欣赏以一种积极的方式扩展。

所以，事不宜迟…让我们开始我们的旅程。

#### **目录**

页面检查器|形状路径编辑器|滤镜|动画|网格|字体|截图 **|** 样式编辑器|响应设计模式|转换|使用预处理程序|变量自动完成|未来|链接&资源

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### **页面检查器**

[![](img/6680ae362de740f2a71884857941349b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qXrX116e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Afxz-LUtVdYS18GYyJrnY-A.png) 

<figcaption>来自火狐的 3 窗格 DevTools 页面检查器</figcaption>

从版本 62 开始，开发人员可以通过 Firefox 新增加的 3 窗格检查器来检查和修改页面的 HTML 和 CSS。

第三个窗格允许深入了解影响页面特定节点的布局属性，如框大小、位置、显示以及其他功能，如动画、计算和字体。从视觉上来说，这是一个很好的方法，让所有需要的信息都可以被访问和组织，这有助于保持你的 CSS 工作流的效率。

> ![unknown tweet media content](img/178af69896a7774018f787d6ba7d7595.png)![Stuart Robson profile image](img/268e80c65ce101303533fa9beee5dfcc.png)Stuart Robson[@ sturobson](https://dev.to/sturobson)![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)I am _ loving _ 这个三重窗格视图在[@ Firefox nightly](https://twitter.com/FirefoxNightly)
> 
> 的开发者工具中… [当我们在页面上获得网格布局的多选时，它会更加有用。
> 
> 伟大的](https://twitter.com/FirefoxNightly) [@FirefoxDevTools](https://twitter.com/FirefoxDevTools) 工作正在进行。2018 年 05 月 04 日下午 18:54[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=992477510830100482)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=992477510830100482)25[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=992477510830100482)101

**Docs**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector)

### **形状路径编辑器**

[![](img/9f83126c156d5bc0ae91d417fd82290c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xs8gHOyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AeMd-lTZpQUaS_wOMX520-A.png) 

<figcaption>图片由 MDN docs 提供。</figcaption>

创建圆形、矩形、椭圆形或多边形等复杂形状的能力正是 clip-path 的初衷。如果你今天正在试验或使用它，我有个好消息，因为 Firefox 允许你直接从他们的开发工具中编辑和检查形状路径。

> ![unknown tweet media content](img/d1b99cd368d1203f89a5ee0ed00c863e.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1006573229933121536/pu/vid/640x360/iXCoIzxP6A1uQ5hm.mp4?tag=3" type="video/mp4"></video>![Jen Simmons profile image](img/0348627b30ede136b3ffb5d3c6f0e240.png)Jen Simmons@ Jen Simmons![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)我很自豪地向大家展示一系列设计工具中的下一款——火狐形状路径编辑器。看一看:[youtu.be/w8pksaGhjfA](https://t.co/7dXaycRI5P)2018 年 6 月 12 日下午 16:27[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1006573790254338048)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1006573790254338048)370[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1006573790254338048)1155

*Jen Simmons' announcement of the shape-path editor*

你可以在这里观看 Jen 的[完整视频，并使用下面的演示来亲眼看看它是如何工作的:](https://youtu.be/w8pksaGhjfA) [https://codepen.io/dudleystorey/embed/FvJDB?height=600&default-tab=result&embed-version=2](https://codepen.io/dudleystorey/embed/FvJDB?height=600&default-tab=result&embed-version=2)
加载演示并打开 Firefox DevTools

1.  使用 DOM 检查器选择元素(本例中是实际的图像)
2.  通过 DOM 检查器选择元素后，使用 CSS 规则窗格查找元素形状值
3.  单击之前提到的属性值旁边的图标，以在浏览器的视口中高亮显示形状路径。要停用形状路径编辑器，再次点击图标

[![](img/998ef15f6d7ed0a59b57f97b2fd308ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b9BgsHJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7zO81NIuGGlvoCDHkTmqVA.png) 

<figcaption>通过 Firefox DevTools 绘制路径轮廓。图片由 MDN Docs</figcaption>

提供

一旦形状路径在视口中被激活，你将得到更多的方面来帮助描述你的形状路径的特殊特征；特别是实线和虚线。

这些线条将拥有手柄，如果需要进一步调整，您甚至可以用光标来拉伸形状。

很酷吧？

**文档**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Edit _ CSS _ shapes](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Edit_CSS_shapes)

### **滤镜**

如果你不熟悉滤镜，它是一个很棒的 CSS 属性，可以创建模糊、饱和、棕褐色等视觉效果。这些滤镜就像您通常在 Photoshop 中使用的滤镜一样，只是用于浏览器。

从 Firefox DevTools 查看的 [![](img/d3fe8b9260b13da6fdc0e74a9da013a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W16L7hDj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATYOKHvgqWVQ1u8DNk7paMA.png) 

<figcaption>CSS 滤镜。照片由 MDN</figcaption>

提供

当使用 Firefox 检查 CSS 过滤器时，会显示一个带有灰色和白色填充的圆形图标，也可以点击该图标来编辑属性及其值。

火狐 DevTools 提供的 [![](img/59df7be9dd7f1b79a1e558601774228b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OKuV2SzB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgfK0Kl80ZIFbGkSC98uoJw.png) 

<figcaption>CSS 滤镜编辑器。图片由 MDN</figcaption>

提供

单击此样本图标将打开一个过滤器编辑器，您可以添加、移除、调整和更改过滤器的顺序，因为顺序可能会扭曲预期的结果。
[https://www.youtube.com/embed/bHcfLlZE8T8](https://www.youtube.com/embed/bHcfLlZE8T8)
*从 Firefix DevTools 保存 CSS 滤镜。MDN* 提供的视频

当您创建滤镜并对结果感到满意时，您可以将其存储为自定预设。若要启用任何存储的滤镜预置，请使用检查器选择您的元素，点按“样式”面板中前面提到的图标，然后从“滤镜编辑器”窗口中选择您的预置。

**文档**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Edit _ CSS _ filters](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Edit_CSS_filters)

### **动画**

你可能已经注意到动画在这里停留，并且有很好的理由。这是一种被广泛接受的方法，可以帮助吸引用户，并为界面创造一种更动态的方式，为体验注入活力。[动画视图](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/UI_Tour#Animations_view)给出了应用于所选元素的动画的全部细节，以及控制器和时间线(每 250 毫秒标记一次),以处理调试/增强的回放。您还可以控制应用于伪元素的任何动画，如::before 或::after。

[![](img/c465b9fd7de24ea29733684b84e8bb19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Af98_NvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2gtEIkZJgWBjSK3bp3Z3IQ.png)

<figcaption>Firefox DevTools 的动画标签</figcaption>

要开始使用此功能，请找到 DevTools 的“**动画**选项卡。注意颜色编码有助于开发人员理解所应用的动画类型。蓝色动画条表示过渡，橙色表示@关键帧，绿色表示 Web 动画 API。如果一个条包含一个闪电图标，这意味着属性是使用合成器线程激活的。如果动画或过渡有延迟，它将显示为交叉阴影填充。将鼠标悬停在每个栏上会显示一个工具提示，提供有关动画类型、持续时间、延迟和计时功能(缓动)的信息。
[https://www.youtube.com/embed/9fxRm3i_aDQ](https://www.youtube.com/embed/9fxRm3i_aDQ)
*动画检查器。MDN docs* 提供的视频

在动画窗格的顶部，为您提供了用于回放、暂停、重启以及调整速度和时间的时间轴控件。还有一个搓擦条，用于手动将回放调整到特定的时间点，以便进一步分析。

**文档**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Work _ with _ animations](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Work_with_animations)

### **网格**

我只能假设你们大多数读者一直在关注 CSS 布局这些年来的巨大改进，尤其是 Grid。Firefox 是第一批为开发人员提供强大工具的浏览器之一，以便使用 Grid 检查元素，而且随着时间的推移，它只会越来越好。

[![](img/57e6021d3d5d937341a70eaa8fc1d8d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hE1_OTYD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ark9PnSmx9PHcGb_jYGC7hw.png) 

<figcaption>Firefox DevTools 网格检查员</figcaption>

为了检查应用了 grid 属性的元素，DOM inspector 将在这些元素旁边显示标有单词“ **grid** 的元素。在“样式”窗格中，元素的属性值旁边还会有一个附带的网格图标。单击此图标将在所选元素或区域的顶部显示一个网格覆盖图，从而显示应用于网格区域的所有信息，如线条和轨迹。查看下面的演示，进一步了解它为检查 CSS 网格提供的强大功能:
[https://codepen.io/GeorgePark/embed/bLLzJK?height=600&default-tab=result&embed-version=2](https://codepen.io/GeorgePark/embed/bLLzJK?height=600&default-tab=result&embed-version=2)
*Trello 板布局与 CSS 网格& Flexbox 作者:George W. Park*

为了进一步增强已经非常强大的网格检查系统，Firefox 提供了显示行号、区域名称和延长线的选项，您可以在闲暇时打开或关闭这些选项，还可以定制网格区域的高亮颜色。Firefox 的一个真正强大的特性，也是我们网格狂热者的先驱。

> ![unknown tweet media content](img/770d36ab6366a3f808be3409cbfb9683.png)![Gabriel profile image](img/b67dfe55c9817da2a0b797f4a5c0bd52.png)Gabriel@ Gabriel loung![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)你将能够在 [@FirefoxDevTools](https://twitter.com/FirefoxDevTools) (将于 64 年发货)中高亮显示多个网格。出于性能原因，当前限制设置为 3，但是，您可以随时在 devtools . grid inspector . max highlighters pref🥳20:53pm-08 2018 年 10 月 20 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1049402479618744320)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1049402479618744320)29[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1049402479618744320)80下的 about:config 中将其提高

*Highlight multiple grid regions in Firefox 64*

**文档**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Examine _ grid _ layouts](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Examine_grid_layouts)

### **Fonts**

我是字体设计的超级粉丝，Firefox 用我最喜欢的一个特性进入了他们的 DevTools，这无疑让我大吃一惊；字体标签。

[![](img/6bc90bbc3c699cb9d231efa7d453da12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aYyLIHwf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/923/1%2AEOeHnjbNJW131_uHXAy3hg.png) 

<figcaption>火狐 DevTools 字体标签</figcaption>

“字体”选项卡使开发人员能够检查、查看和操作应用于页面的字体以及其他方面，如行高、粗细、字体系列和字体样式。还为您提供了更深入的信息，比如正在使用的字体的 URL 路径、@font-face 声明和字体系列的文本样本。

> ![unknown tweet media content](img/1dc72e579973c7a08b1142aebb9d4e84.png)![Jenna Hines profile image](img/f4de1c9c665de8abc802efc4a1d6d34a.png)Jenna Hines@ jhines 24![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)TIL[@ Firefox dev tools](https://twitter.com/FirefoxDevTools)为您的字体堆栈中正在使用的字体加下划线。2018 年 05 月 08 日 23:52 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=994002082356178944)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=994002082356178944)82[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=994002082356178944)344

*Gain immediate insight on what fonts are in use for your stack*

从版本 62 开始，也支持可变字体，从而允许您在浏览器中实时检查它们。
[https://codepen.io/jpamental/embed/PebXEX?height=600&default-tab=result&embed-version=2](https://codepen.io/jpamental/embed/PebXEX?height=600&default-tab=result&embed-version=2)
*杰森·帕门塔尔用火狐探索可变字体*

如果你不熟悉可变字体，它们允许字体设计者在一个单独的字体文件中包含多种不同的字体。如果你对可变字体感兴趣，请查看这个资源做进一步的研究。

[![](img/353211a103a5ce137786ecf06a361528.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b1v6f85l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/889/1%2Aod3W-0GEmZlHHh-n9vLaTQ.png) 

<figcaption>显示用于调整可变字体的控件</figcaption>

如果正在使用可变字体，字体选项卡将为执行的轴提供额外的控制，允许您控制和改变每个轴的值。这有助于发现哪些轴是可用的，因为它们可能变化很大，因为设计人员可以实现各种各样的选项。

您可以单独调整轴，或者如果字体设计器包含已定义的实例，您可以从“**实例**”下拉列表中选择一个实例，并在您的页面上实时查看更新。如果你喜欢字体，那么 Firefox DevTools 是你的最爱。看看这个奇妙的可变字体游乐场以及 http://play.typedetail.com。
[![](img/29b431b03c9e1bec4a302b8035a84c2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NKv39xkQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7nzsubhkg37l2k8igwrz.png) 

> ![unknown tweet media content](img/56e95c2a33761c71a6faca0293f7b28f.png)![Ethan Marcotte profile image](img/f4f68be571958d5d01ce8280f6d5bc68.png)伊森·马科特@叭![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)我被火狐中的新字体面板深深地迷住了，是的没错我被深深地迷住了2018 年 10 月 16 日下午 16:08[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1052229783726247939)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1052229783726247939)91[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1052229783726247939)

**Docs** :

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Edit _ fonts](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Edit_fonts)

### **截图**

[![](img/aa014dce15bc30ce65fe28278e155fab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HhM98U7b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8QCoS5EuHbikoi-2QzPHQw.png)

如果你的工作需要以静态形式记录界面，Firefox 已经很好地实现了可视化记录你的工作的巧妙方法。除了获取完整页面截图的能力，您还可以选择部分页面区域，甚至单个节点(对于开发独立组件/模块的作者来说，这是一个方便的特性)。

用 Firefox 完成截屏有几种方法；通过 DevTools 本身，或者使用浏览器栏。如果使用 DevTools，直接选择“**设置、**，找到标题为“**可用工具箱按钮**的栏，找到切换按钮“**对整个页面进行截图**

[![](img/4c6f0baa17988c15cf936e9b859ed4b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XuG2LnTY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/224/1%2AxWlNdgXUo3_xo67n007yAA.png) 

<figcaption>通过 DevTools 设置提供的截图行为选项</figcaption>

在“**屏幕截图行为**下，您会发现控制…等待…行为的附加选项。选择这些选项后，DevTools 菜单栏将在右上角显示一个摄像头图标，允许您点击视窗，并对整个页面进行截图。如果你不想要整个页面的快照，你也可以使用你的 HTML 检查器面板，通过右击你选择的节点并选择“**屏幕截图节点**”来捕捉单个元素

[![](img/a51168aeb360e008bafd9e113fc1db22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ZDgNOX0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/852/1%2AM3XykTuKYnH33da7gCXYKA.png) 

<figcaption>Firefox 地址栏的页面动作菜单</figcaption>

[![](img/20c1242c694fb41dde442d693979f956.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ysVdMR78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/382/1%2AB3YTjDRjzRt4jDV8QM3Rhw.png) 

<figcaption>在火狐的地址栏</figcaption>

的页面动作下提供截图选项

获取屏幕截图的另一个选项是选择浏览器地址栏中由 3 个点代表的**页面动作**。当点击这三个点时，你会注意到一个下拉菜单，显示文本“**截图**此选项允许拍摄整个页面、可见区域或使用光标高亮显示单个元素。

[![](img/c542f951a9485f15c61e5ba7011cc9ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IA7OjQY_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AL423p1_LtK9CHP2i06poEg.png)

截屏捕获后，您可以将其复制到剪贴板，直接下载，作为 URL 共享，或保存到浏览器。如果你在本地保存到浏览器，你可以选择一段时间来终止截图或无限期保存。如果您需要访问您保存的照片，请单击浏览器栏中的库图标，然后选择“**截图**”如果控制台是创建截图的首选，你可以运行以下命令……

```
screenshot <your-desired-filename.png> — fullpage 
```

Eric Meyer 也写了一篇很棒的文章，关于你可以在控制台中传递给屏幕截图命令的额外标志，我强烈建议阅读。

**文档:**

*   [https://screenshots.firefox.com](https://screenshots.firefox.com/)
*   [https://developer . Mozilla . org/en-US/docs/Tools/Taking _ snapshot s](https://developer.mozilla.org/en-US/docs/Tools/Taking_screenshots)

### 样式编辑器

样式编辑器并不是什么新东西，但是它和我到目前为止提到的任何其他特性一样有价值。大多数 CSS 作者最终需要检查整个文件，甚至将更改保存回磁盘。

[![](img/59438d912786cfaf66a756968efd0132.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A89M_WA8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/809/1%2AjcZbJGfpZ1cVQzySakxtWQ.png) 

<figcaption>Firefox DevTool 样式编辑器。图片由 MDN docs</figcaption>

提供

查看、创建和导入整个样式表只是几个方面。
[https://www.youtube.com/embed/XeocxoW2NYY](https://www.youtube.com/embed/XeocxoW2NYY)
*
右边的侧边栏专门用于那些讨厌的@media 规则，它提供了到定义规则的样式表的链接，以及导入自定义样式表或创建新样式表的能力。

> ![unknown tweet media content](img/0beee20bf3563d6f16c964c70a4bf1f6.png)![Firefox DevTools profile image](img/08c0bc67828cafbe7cf641a93ff05d08.png)Firefox DevTools@ Firefox DevTools![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)你用 Firefox DevTools 里的样式编辑器面板吗？如果有，你想用它来解决什么问题？
> 
> 我们希望您能在这份由 3 个问题组成的调查中提出意见:
> [goo.gl/forms/tNzfD841…](https://t.co/YiPwHEayWG)
> 
> 谢谢！2018 年 10 月 25 日下午 14:00[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1055459007933104129)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1055459007933104129)36[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1055459007933104129)38

**Docs:**

*   [https://developer.mozilla.org/en-US/docs/Tools/Style_Editor](https://developer.mozilla.org/en-US/docs/Tools/Style_Editor)

### 响应式设计模式

响应式设计对开发者来说并不新鲜，因为它是我们每天都要处理的事情；尤其是涉及到我们 CSS 的时候。能够研究屏幕大小、设备甚至网络速度无疑是开发网页最重要的方面之一。

[![](img/e451e655476272f6f82c810c4695476e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n7YIU2Fj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbMF00aDI05tgVnYYh4HObg.png)

<figcaption>Firefox dev tools 中的响应式设计模式。图片由 MDN docs 提供。</figcaption>

Firefox 非常注意为开发人员提供强大的工具来检查站点的响应方面，并在最近几年从 Chrome 那里获得了很大的指导。从 CSS 作者非常关心的各个方面进行选择，比如设备、像素比例、用户代理、自定义屏幕尺寸、方向、网络节流，当然还有获取屏幕截图。您甚至可以点击@media 规则，在响应式设计模式下自动应用它。

**文档:**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Responsive _ Design _ Mode](https://developer.mozilla.org/en-US/docs/Tools/Responsive_Design_Mode)

### 变换

不用猜测就能确切地看到变换的位置，这不是很好吗？有了 Firefox DevTools，你可以！

[![](img/6077d9314aa56a8abefb2240ebf56b09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2fC5SGUo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A10u86THSU_UFX1rpX-954A.png) 

<figcaption>在 Firefox DevTools 中变换可视化。图片由 MDN Docs 提供。</figcaption>

要查看这种可视化效果，将鼠标悬停在任何转换属性上，您会看到转换在页面上以覆盖图的形式出现。

**文档:**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Visualize _ transforms](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Visualize_transforms)

### 使用预处理器

这是我们这些天非常熟悉的一个特性，但它值得重复。我们许多 CSS 作者都在使用某种形式的预处理语言，比如 Sass 或 LESS，这就是为什么处理这些文件和将更改保存回本地磁盘同样重要。
[https://www.youtube.com/embed/zu2eZbYtEUQ](https://www.youtube.com/embed/zu2eZbYtEUQ)
在[样式编辑器设置](https://developer.mozilla.org/en-US/docs/Tools_Toolbox#Style_Editor)中选择“显示原始来源”。在[规则视图](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector#Rules_view)中 CSS 规则旁边显示的链接将链接到样式编辑器中的原始源代码。请确保为您选择的语言启用了源映射，因为此功能对于任何不支持源映射的抽象语言都不可用。

**文档:**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Style _ Editor # Source _ map _ support](https://developer.mozilla.org/en-US/docs/Tools/Style_Editor#Source_map_support)
*   [https://hacks . Mozilla . org/2014/02/live-editing-sass-and-less-in-the-Firefox-developer-tools](https://hacks.mozilla.org/2014/02/live-editing-sass-and-less-in-the-firefox-developer-tools/)

### 变量自动完成

CSS 变量名最终能够根据 CSS 中定义的变量自动完成。当您在属性值中键入 var(后跟破折号(-)时，CSS 中定义的任何变量都将出现在自动完成列表中，同时还会出现一个色样，以直观的方式显示每个变量存储的颜色。

[![](img/4fb51b54b4c7e8806a2c99e2b6c710ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sTfNY_kB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/546/1%2AHUsso7Wq32eOaU_Om8rt7w.png)

<figcaption>Firefox dev tools 中的 CSS 变量自动补全。图片由 MDN Docs 提供。</figcaption>

除了自动完成功能之外，将鼠标悬停在任何 CSS 变量名上都会显示一个工具提示，显示存储了什么颜色值。

**文档:**

*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Examine _ and _ edit _ CSS # CSS _ variable _ autocompletion](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Examine_and_edit_CSS#CSS_variable_autocompletion)

### 未来

Firefox DevTools 团队无意在增强和改进方面放慢他们的进度。未来是光明的，许多令人兴奋的方面即将到来。这里只提到一些与 CSS 创作相关的改进。

> ![Chris Ruppel profile image](img/72959323a5b439346b6c3b6668c7b0c9.png)克里斯·鲁佩尔@ rupl![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)如果你用网络做任何面向设计或有创意的事情， [@FirefoxDevTools](https://twitter.com/FirefoxDevTools) 绝对领先群雄！自从 Quantum 发布以来，它已经很快成为我的首选。2018 年 2 月 02 日下午 13:02[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=959411692596793344)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=959411692596793344)12[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=959411692596793344)37

#### Ch-Ch-Ch-变化

CSS 变更跟踪是一个显示 DevTools 最近所做变更的特性，以便跟踪您的进度。在撰写本文时， **Changes** 面板隐藏在 Firefox Nightly 中 about:config 下的一个标志后面。

```
devtools.inspector.changes.enabled 
```

一旦启用了该标志，您就可以将您的更改与原始代码行进行比较。这些更改用加号或减号图标表示，以表示添加/删除了什么，以及声明属于哪个样式表。我只希望将来也能包括行号。

> ![unknown tweet media content](img/15157b56b7f1133c239378e7041c6650.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1057746539471286272/pu/pl/RikFGVhaJsAL0B9n.m3u8?tag=5" type="application/x-mpegURL"></video>![Firefox DevTools profile image](img/ea0714a1c4d24aabe82c9da2921f57b9.png)Firefox devtools@ Firefox devtools![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)✨每夜新功能:在 about:config:dev tools . inspector . changes . enabled 中启用此 pref，尝试我们的第一版 CSS 更改跟踪功能。我们迫不及待地想听听听听您的想法！[mozilla.org/en-US/firefox/…](https://t.co/1G90bMLSEk)
> 
> UX:[@ viola song](https://twitter.com/violasong)
> Dev:[@ razvancaliman](https://twitter.com/razvancaliman)+Brad Werth
> PM:[@ martinbalfanz](https://twitter.com/martinbalfanz)2018 年 10 月 21:42![Twitter reply action](img/269095962147c28351274afdd5486a48.png)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1057749744032743425)103[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1057749744032743425)

> ![unknown tweet media content](img/df8a56df858bc7a42a084e4436b16294.png)![Scream Queen Stu Robson profile image](img/268e80c65ce101303533fa9beee5dfcc.png)尖叫女王斯图罗布森[@斯图罗布森](https://dev.to/sturobson)![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)这个新的‘变化’面板在 [@FirefoxNightly](https://twitter.com/FirefoxNightly) 是😍
> 
> 在地址栏- about:config
> 
> 搜索-devtools . inspector . changes . enabled
> 
> 双击去伪存真2018 年 11 月 13 日上午 10:55[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1062297806247997441)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1062297806247997441)31[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1062297806247997441)88

#### Flexbox 检查器

[![](img/0c9497b02b2eba2ed1163160db416fb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TEwOaK6o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/250/1%2AJxMjwYDjBQ7MLNRoeXPWWQ.png) 

<figcaption>通过 Firefox DevTools 查看 Flexbox 检查器。</figcaption>

如果你正在使用 flexbox，你可能会遇到事情不太清楚的时候，这就是为什么这个下一个特性(目前在每晚写这篇文章时)让我兴奋。

> ![unknown tweet media content](img/495f1f7783dab6646bd03668cf8a3bf9.png)![Firefox DevTools profile image](img/ea0714a1c4d24aabe82c9da2921f57b9.png)Firefox dev tools@ Firefox dev tools![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)“为什么这个 Flex 项这么宽？”
> 
> 使用 [@FirefoxNightly](https://twitter.com/FirefoxNightly) 中新的 Flexbox Inspector 来了解一下吧！
> 
> -[@ patrickbrosset](https://twitter.com/patrickbrosset)2018 年 11 月 09 日下午 15:30[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1060917496734195715)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1060917496734195715)108[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1060917496734195715)388

您需要了解您的 flex 项目是如何布局的吗？有了新的可视化工具，只需快速浏览一下，就能了解原因和方法。

> ![unknown tweet media content](img/abf04df4bccbdc1f03a620913f83a266.png)![𝕡𝕒𝕥𝕣𝕚𝕔𝕜 profile image](img/6f4ec25c2d6a98648d014bf760e49b92.png)𝕡𝕒𝕥𝕣𝕚𝕔𝕜@ patrickbrosset![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)我正在 [@FirefoxDevTools](https://twitter.com/FirefoxDevTools) 中开发一些真正令人兴奋的 CSS flexbox 工具。我迫不及待地想让它们在 FirefoxNightly 中可用，这样你们就可以试用它们了！
> 
> 先睹为快:2018 年 10 月 05 日下午 14:20[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1048216264928362496)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1048216264928362496)111[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1048216264928362496)358

#### 分享您的意见

Firefox DevTools 非常关心并希望你的声音被听到。这里有一篇很棒的文章，要求 [**你输入**](https://hacks.mozilla.org/2018/11/new-experimental-web-design-tools-feedback-requested) **。**

> ![unknown tweet media content](img/e0c9918ab3f428780149e230f895077e.png)![Victoria Wang profile image](img/1431cf9334e620a6f44453523ab78f61.png)王维@ violasong![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)🦊🛠️ UX 贡献者更新:我们现在在 github.com/devtools-html/…[有一个家](https://t.co/FE6WVtFP8i)有 15 个新任务，我们刚刚做了我们的第一个社区调用:d21:26pm-2018 年 8 月 23 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1032740771764436992)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1032740771764436992)12[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1032740771764436992)34

提供反馈或帮助的其他方式可以通过团队的活跃[松弛渠道](https://devtools-html-slack.herokuapp.com/)以及他们的[对话](https://discourse.mozilla.org/c/devtools)来完成。有很多人，付费的和自愿的，把他们的努力和爱倾注到 Firefox DevTools 中。如果你在某些方面有问题，你也可以发微博到 [@FirefoxDevTools](https://twitter.com/FirefoxDevTools) 或[参与进来](https://firefox-dev.tools.)。

### 离别的思念

不管你对开发工具的选择是什么，事实是清楚的；任何时候一个 DevTool 改进或者想要变得更好，我们作为一个社区都会受益。由于跨浏览器测试如此重要，所以拥有一个 DevTool 是有意义的，它可以帮助我们改进工作流程，并继续使日常任务变得不那么费力，从而使我们的注意力保持在真正重要的事情上；动态调试、检查、保存和调整。

以下是其他供应商的一些有用链接，这样他们也能听到您的声音:

*   **Edge**:[https://docs . Microsoft . com/en-us/Microsoft-Edge/dev tools-guide # feedback](https://docs.microsoft.com/en-us/microsoft-edge/devtools-guide#feedback)
*   **Chrome**:[https://developers . Google . com/web/tools/Chrome-dev tools/# community](https://developers.google.com/web/tools/chrome-devtools/#community)
*   **野生动物园**:【https://developer.apple.com/bug-reporting T2】
*   **Opera**:[https://developers . Google . com/web/tools/chrome-dev tools/# community](https://developers.google.com/web/tools/chrome-devtools/#community)

### **链接&资源**

*   [https://developer.mozilla.org/en-US/docs/Tools](https://developer.mozilla.org/en-US/docs/Tools)
*   [https://developer.mozilla.org/en-US/docs/Tools/Tips](https://developer.mozilla.org/en-US/docs/Tools/Tips)
*   [https://developer . Mozilla . org/en-US/docs/Tools/Page _ Inspector/How _ to/Examine _ and _ edit _ CSS](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Examine_and_edit_CSS)
*   [https://github.com/devtools-html/ux](https://github.com/devtools-html/ux)
*   [https://Twitter . com/Firefox dev tools/status/974685361342484480](https://twitter.com/FirefoxDevTools/status/974685361342484480)
*   [https://logrocket.com/blog/ultimate-web-design-tool](https://logrocket.com/blog/ultimate-web-design-tool)

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *