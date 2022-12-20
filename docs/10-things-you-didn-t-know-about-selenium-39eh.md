# 关于硒你不知道的 10 件事

> 原文：<https://dev.to/endtest/10-things-you-didn-t-know-about-selenium-39eh>

### **1)你可以用 Selenium 来测试 Chrome 扩展。**

这些扩展是用 HTML、CSS 和 JavaScript 构建的，你只需要学习一些技巧就可以在主视窗中访问这些扩展。几个月前，我写了一篇详细的[教程](https://dev.to/razgandeanu/testing-chrome-extensions-with-selenium-491b)。

你也可以用 [Endtest](//https:/endtest.io) 测试 Chrome 扩展。

### **2)对一个元素而不是整个页面进行截图。**

这个比较棘手。默认情况下，Selenium 只允许你对整个视窗进行截图。
您需要提取元素的坐标和大小，并使用它们从整个页面截图中裁剪元素。

我用[枕头](https://pillow.readthedocs.io)做裁剪部分。🤓

*driver . save _ snapshot('/Users/Klaus/Test/fullpagesnapshot . png ')*
*my element = driver . find _ element _ by _ id(" register ")*
*location = my element . location*
*left = int(location[' x '])*
*top = int(location[' y '])*
right = int(location[' x '])+int(size['大小

### **3)在测试中上传文件。**

我从朋友那里学到的一个很酷的技巧。

当您在网站上上传文件时，操作系统会打开一个本机窗口，允许您选择文件。

[![](img/43cee47808e3d7f8120d8880dcb9d53d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MXbjNTcf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ck5zymf6ig5h0ohcyqgt.png) 
从本机窗口中选择文件后，文件的本地路径被写入输入 type="file "元素中。

这里问题是 Selenium 不能与本机窗口交互。
这意味着它不能选择文件。

这就是为什么我们需要跳过这一部分，只在 input type="file "元素中写入该文件的本地路径。

*file input = driver . find _ element _ by _ id(" upload _ file ")*
*file input . send _ keys('/Users/Klaus/Test/instructions . pdf ')*

当然，input type="file "元素可能是隐藏，您需要使用一些 JavaScript 来取消隐藏:

*JScode = ' document . GetElementBYID(" # upload _ file ")style . display = " block "*
*driver . execute _ script(JScode)*

### **4)点击一个元素内部的某个点。**

默认情况下，Selenium 单击元素的中心。

当处理一个 [HTML5 Canvas](https://www.w3schools.com/html/html5_canvas.asp) 元素时，你可能需要更多的精度。

我们可以使用 Selenium 中的 ActionChains 移动到元素内部的某个点，并在那里执行单击。

*canvas = driver . find _ element _ by _ id(" drawing _ board ")*
*chain = action chains(driver)。move _ to _ element _ with _ offset(canvas，X，Y)*
*chain . click()*
*chain . perform()*

没错，你可以用硒在线下棋。♟️

### **5)阅读页面来源。**

如果你的网站正在使用标签，你可以很容易地检查那些带有 Selenium 的标签。

*page source = driver . page _ source*

如您所知，标签是隐藏的，只能在页面源代码中看到。

### **⑥)执行拖拽。**

我们再次使用 Selenium 的动作链。

*target = driver . find _ element _ by _ id(' ball ')*
*destination = driver . find _ element _ by _ id(' goal ')*
*action chains(driver)。拖放(目标，目的地)。执行()*

我们刚刚用硒进球了吗？⚽️😱

### **7)模拟网络摄像头和麦克风。**

如果您在一台来自云的机器上运行您的测试，这将非常有用。
仅适用于 chromedriver。

[![](img/a87b7f7de1f0cdb02663456455b04db1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rEDEyhNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/95qbfh4qv6l8gqpyzad0.png)

*选项=网络驱动。chrome options()*
*options . add _ argument("-use-fake-ui-for-media-stream ")*
*options . add _ argument("-use-fake-device-for-media-stream ")*
*driver = web driver。chrome(executive _ path = chrome driver，chrome_options=options)*

### **8)使用 HTTP 基本认证。**

这是一个本机窗口，要求提供访问网站的凭据。

[![](img/ce7cb5061364b8c2c75dade8e6d76225.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BuZE561x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kcow15ktmw01jntr6grb.jpg)

你不能用传统的 *send_keys* 方法在那些输入中写文本，因为那个窗口在视窗之外。

解决办法？

只需直接在 URL 中添加您的用户名和密码。

*driver . get([http://username:password @ example . com '](http://username:password@example.com'))*

### **9。使用机器学习来修正你的测试。**

[https://www.youtube.com/embed/t7Wa5mTP45Q](https://www.youtube.com/embed/t7Wa5mTP45Q)

### **10。使用任何属性来定位元素。**

Selenium 允许您通过以下方式之一定位元素:
ID
名称
类名
链接文本
部分链接文本
标记名
CSS 选择器
XPath

但是当你有这样一个元素时会发生什么呢？

[![](img/416c5cd34c46cf51cbb19fb890e22d5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NtRAv6_g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbol1ucy4za0oqkximuf.png)

您需要发挥创造力，根据属性编写一个 XPath，就像这样:

//*[@attribute = "属性值"]

对于我们的元素，这会导致以下结果:

//*[@type = "submit"]

您还可以编写一个 XPath，通过仅使用属性的部分值来定位元素:

//*[包含(@attribute，"属性值的一部分")]

对于我们的元素，这会导致以下结果:

//*[包含(@type，" sub")]