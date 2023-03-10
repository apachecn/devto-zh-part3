# 开发人员对过去两周的回购进行总结

> 原文：<https://dev.to/devteam/devto-repo-recap-from-the-past-two-weeks-1j4a>

再次回顾。这一期将涵盖过去两周——12 月 2 日至 12 月 15 日——所以系好安全带！

# 特性

*   感谢 [@rkichenama](https://dev.to/rkichenama) 开启夜间模式！请注意，它仍处于早期阶段，需要更多的发展。查看下面的帖子了解更多信息。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1104)
    *   [second iteration pr](https://github.com/thepracticaldev/dev.to/pull/1303)[![somedood](img/6986f9aa793e50222c10a95749f28d12.png)](/somedood) [## Preview the upcoming night mode

        of dev.to

        ### Basti Ortiz read

        # meta](/somedood/previewing-devtos-upcoming-night-mode-44j)in 182 minutes on December 9th
*   您现在可以在您的个人资料中添加乳齿象网址了！感谢 [@citizen428](https://dev.to/citizen428) 的公关。我们目前允许一个特定的网址列表，所以如果你想添加一个，请随意制作一个 PR。

    *   [链接到@citizen428 的公关](https://github.com/thepracticaldev/dev.to/pull/1137)
*   与此相关，这里有一个添加两个乳齿象实例的例子——Ruhr . social 和 chaos . social——感谢 [@timkrueger](https://dev.to/timkrueger) 。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1260)
*   [@mrtnrdl](https://dev.to/mrtnrdl) 用他的 PR 添加了 infosec.exchange 乳齿象实例；谢谢你，马丁！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1262)
*   乳齿象实例 toot.cafe 是由 [@nickytonline](https://dev.to/nickytonline) 添加的——谢谢你，尼克！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1268)
*   [@wuz](https://dev.to/wuz) 将 radical.town 加入乳齿象列表。谢谢你，科林！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1279)
*   [@jibrankalia](https://dev.to/jibrankalia) 增加了一个“非常简单的计算阅读时间功能。”它目前只在后端可用，但这允许我们在其上进行构建。谢谢你，吉卜兰！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1283)
*   [@ben](https://dev.to/ben) 为 iOS 添加了一些触觉反馈消息，在某些情况下会触发，比如留下一颗心。查看 PR 了解更多详情:

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1287)
*   [@ben](https://dev.to/ben) 也对帖子的“动作栏”的位置做了一些改动。如果你在 1530 像素或更高的桌面屏幕上，你会看到动作条在边上。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1288)

# Bug 修复/其他贡献

*   感谢 [@markfrst](https://dev.to/markfrst) 修复了阅读列表页面的页脚。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/435)
*   @joshichinmay 修复了提要中某些文本重叠的边缘情况。谢谢，Chinmay！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/502)
*   感谢 [@jacobherrington](https://dev.to/jacobherrington) 在我们的自述中去掉了一个额外的步骤。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1148)
*   感谢 [@mariocsee](https://dev.to/mariocsee) 修复了几个连接 bug。第一个问题是邀请已经在频道中的成员加入频道。第二个是修复了一个频道细节中的个人资料图像渲染问题。第三个涉及重构轮廓图像的呈现方式。第四个修正了突出显示文本的问题。

    *   [第一次 PR 提到](https://github.com/thepracticaldev/pull/1201)
    *   [第二次 PR 提到](https://github.com/thepracticaldev/pull/1247)
    *   [第三次 PR 提到](https://github.com/thepracticaldev/dev.to/pull/1255)
    *   [第四次公关提到](https://github.com/thepracticaldev/dev.to/pull/1311)
*   我们修正了一些关于通知的错误-更多细节请见 PR。如果您在通知中看到任何错误，请告诉我们！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1232)
*   感谢 [@Link2Twenty](https://dev.to/link2twenty) 修复了手机中阅读列表头的一个 bug！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1237)
*   @ doestyphopp 修复了 GitHub gists 在移动视图中的呈现方式——标签尺寸太大！现在它们大小合适了。谢谢你泰。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1251)
*   Rozie 着手修复了由 [@Link2Twenty](https://dev.to/link2twenty) 造成的一个问题，即保存按钮的对比度不符合可访问性标准。谢谢， [Rozie](https://github.com/rozenraymond) ！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1261)
*   感谢 [@tzemanovic](https://dev.to/tzemanovic) ，个人[开发徽章页面](https://dev.to/p/badges)现在使用 FontAwesome 徽章！

    *   [特性测试 PR](https://github.com/thepracticaldev/dev.to/pull/1266)
    *   [文件重组 PR](https://github.com/thepracticaldev/dev.to/pull/1317)
*   [@lightalloy](https://dev.to/lightalloy) 重构了很多与提要相关的视图。安娜还为他们增加了一堆功能测试。非常感谢，安娜！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1269)
*   安娜也更新了我们的`rubocop_todo.yml`文件。如果你有一些 Ruby 和 Rails 的知识，这是一个不错的起点。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1270)
    *   [链接到 rubocop . yml 文件](https://github.com/thepracticaldev/dev.to/blob/master/.rubocop_todo.yml)
*   感谢 [@Link2Twenty](https://dev.to/link2twenty) 修复了一些辅助功能问题:

    *   [变暗 CTA 按钮以获得更好的对比度](https://github.com/thepracticaldev/dev.to/pull/1281)
    *   [将文本替换为主页中的图像](https://github.com/thepracticaldev/dev.to/pull/1272)
*   [@押韵](https://dev.to/rhymes)改变了一些语言:我们现在用`allow/allowed`代替`whitelist`。谢谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1296)
*   [@Link2Twenty](https://dev.to/link2twenty) 做了一个使用 CSS 变量的 PR 用于夜间模式。这是为未来做好准备的一个好步骤。谢谢安德鲁。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1307)
*   @maestromac 重构了一些与工作相关的代码，因为我们将慢慢迁移到 Rails 的 ActiveJob。PR 中的更多详细信息:

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1312)
*   [@韵脚](https://dev.to/rhymes)为系列文章的链接增加了一个有用的`title`属性。再次感谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1329)
*   [@anant90](https://dev.to/anant90) 修复了作者简历溢出`<div>`的问题。谢谢你，阿南特！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1300)

# 新问题/讨论

*   [@bennypowers](https://dev.to/bennypowers) 为更好的从右到左语言支持打开了一个功能请求。谢谢你本尼。查看这一期的讨论。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更好的 RTL 语言支持 #1239](https://github.com/thepracticaldev/dev.to/issues/1239) 

    [![bennypowers avatar](img/9efcc06e158922dcdfa5bdbf999b547d.png)](https://github.com/bennypowers) **[bennypowers](https://github.com/bennypowers)** posted on [<time datetime="2018-12-02T13:19:12Z">Dec 02, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1239)

    **您的功能请求是否与某个问题相关？请描述一下。在 dev.to 上用 RTL 语言写的帖子和评论看起来并不总是很棒。见[https://dev . to/biros/where-are-dev-users-coming-from-4l 53/comments](https://dev.to/biros/where-are-dev-users-coming-from-4l53/comments)**

    **描述您想要的解决方案** RTL 的帖子和评论应该被识别，并根据需要应用属性或样式。https://github.com/waiting-for-dev/string-direction 见

    **描述你考虑过的替代方案**渴望听到其他想法。

    **附加上下文**参见#304

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1239)
*   [@ wolfghoundjesse](https://dev.to/wolfhoundjesse)请求了一个评论线程嵌入(很可能是通过 Liquid 标签)。谢谢杰西。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 支持评论线程嵌入 #1240](https://github.com/thepracticaldev/dev.to/issues/1240) 

    [![wolfhoundjesse avatar](img/3b9d1cef9de4da96ec9ef436ed728c04.png)](https://github.com/wolfhoundjesse) **[wolfhoundjesse](https://github.com/wolfhoundjesse)** posted on [<time datetime="2018-12-02T19:04:33Z">Dec 02, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1240)

    **您的功能请求是否与某个问题相关？请描述一下。**如果我想嵌入评论线程进行讨论，我必须包含三个独立的评论嵌入:

    ```
    {% devcomment xxx0 %}
    {% devcomment xxx1 %}
    {% devcomment xxx2 %} 
    ```

    这导致三个嵌入有足够的填充，使他们看起来不相关。

    **描述你想要的解决方案**评论线程已经有了 permlinks。也许我们可以:

    ```
    {% devcommentthread xxx0 %} 
    ```

    **描述你考虑过的替代方案**有两个注意事项让这变得有些复杂。

    1.  一个评论线程可能比你想嵌入的要深 20 个评论。

    2.  你可能想挑选评论，不是为了邪恶，但也许你想突出的讨论是在两个人之间，而不是在中间某处插话的其他 12 个人。

    **附加背景**这可能是一个边缘案例，但我将它提交给社区讨论。

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1240)
*   [@Link2Twenty](https://dev.to/link2twenty) 制造了两个与可访问性相关的问题:第一个提到标签耀斑应该有一定程度的对比度才能符合可访问性标准。第二条规定链接必须有可识别的文本。再次感谢你，安德鲁。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  A11y:标记光斑上的对比 #1273](https://github.com/thepracticaldev/dev.to/issues/1273) 

    [![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2018-12-06T13:01:37Z">Dec 06, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1273)

    **描述问题**文章标题中某些标签的对比度太低。

    **重现**如果你有一双好眼睛，很难看出是否有对比度问题。如果你把整个网站过滤成灰度，它可以帮助你发现问题。

    ```
    body {
      filter: grayscale();
    }
    ```

    Enter fullscreen mode Exit fullscreen mode

    [![image](img/d6df6b46a8266dc023e993fb37174fa7.png)](https://user-images.githubusercontent.com/3534427/49635936-189e4a80-f9f9-11e8-9853-4a8e24ef1268.png)

    **附加背景**改变所有颜色以获得良好的对比度值将有助于提高 lighthouse 分数。

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1273)

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  A11y:链接必须有可识别的文本 #1278](https://github.com/thepracticaldev/dev.to/issues/1278) 

    [![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2018-12-07T13:23:04Z">Dec 07, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1278)

    **描述问题**目前对于`article-organization-headline`你有两个链接，一个是`article-organization-headline-inner`一个是`org-headline-filler`，没有内容，这意味着键盘用户必须跳过这个链接，而使用屏幕阅读器的用户将不知道第二个锚是什么。

    拥有一个有风格跨度的 achor 将解决你的问题，你甚至不需要改变任何 CSS。

    **电流:**

    ```
    <div class="article-organization-headline">
      <a href="#" class="article-organization-headline-inner">
        <img src="#">
         Team name
      </a>
      <a class="org-headline-filler" href="#">&nbsp;</a>
    </div>
    ```

    Enter fullscreen mode Exit fullscreen mode

    **建议:**

    ```
    <div class="article-organization-headline">
      <a class="org-headline-filler" href="#">
        <span class="article-organization-headline-inner">
          <img alt="logo" src="#">
          Team name
        </span>
      </a>
    </div>
    ```

    Enter fullscreen mode Exit fullscreen mode

    这又是一件把灯塔评分拉低的小事。[https://dequeuniversity.com/rules/axe/2.2/link-name](https://dequeuniversity.com/rules/axe/2.2/link-name)

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1278)
*   [@sublimemarch](https://dev.to/sublimemarch) 创建了一个允许在帖子中添加多个作者的问题。谢谢你，芬。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 能够给一篇文章添加多个作者 #1299](https://github.com/thepracticaldev/dev.to/issues/1299) 

    [![sublimemarch avatar](img/a727673208b7192e414321a47b2527f9.png)](https://github.com/sublimemarch) **[sublimemarch](https://github.com/sublimemarch)** posted on [<time datetime="2018-12-10T20:36:57Z">Dec 10, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1299)

    作为一个经常与其他开发者合作撰写内容的人，我希望能够在帖子中添加其他作者。目前，我们中的一个人提交了一篇文章，并在文章的正文中提到其他人是作者，但这并不表明作者们享有同等的荣誉。

    该功能可能包括:

    *   能够在帖子创建期间或在用户名发布后添加作者。
    *   一个针对作者的审批机制，这样在没有你同意的情况下，你就不能被添加为一个帖子的作者。
    *   帖子显示在合著者的个人资料上，就好像他们是提交帖子的“主要”作者一样。
    *   帖子作者会在文章上显示他们的个人资料图片和姓名，达到一定的数量(例如，5 个作者)。超过一定的数量，文本如“和其他三个作者”将会出现，用户需要点击它来查看其余的。这是为了保持当前的样式并便于阅读。

    我认为此时没有必要改变帖子的“第一作者”(也就是最初提交帖子的人)，因为这可能需要不同的数据库模式。

    ```
    </div>
    <div class="gh-btn-container"><a class="gh-btn" href="https://github.com/thepracticaldev/dev.to/issues/1299">View on GitHub</a></div> 
    ```

    Enter fullscreen mode Exit fullscreen mode

*   在 V2 编辑器中，@anant90 请求了图片链接旁边的复制按钮。谢谢，阿南特。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 上传完成后，在 Markdown 图片 URL 代码旁添加“复制”按钮 #1313](https://github.com/thepracticaldev/dev.to/issues/1313) 

    [![anantja-in avatar](img/36922396cec802f406083ea54a29225a.png)](https://github.com/anantja-in) **[anantja-in](https://github.com/anantja-in)** posted on [<time datetime="2018-12-12T00:52:42Z">Dec 12, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1313)

    **您的功能请求是否与某个问题相关？请描述一下。**双击 Markdown 图像代码选择它，然后按 Ctrl+C 复制它会减慢用户的速度。

    **描述您想要的解决方案**下方降价图像代码字段旁边的`Copy`按钮:

    [![screen shot 2018-12-11 at 1 18 09 pm](img/8b4b731501dd87e838a6458de40260c2.png)](https://user-images.githubusercontent.com/509059/49831145-05b4ae80-fd48-11e8-9219-9ae1a429ef11.png)

    **描述你考虑过的替代方案**

    1.  在 Markdown 文档中光标所在的位置直接插入 Markdown 图像代码。这可能会让用户感到有点困惑，所以最好不要这样做，而是给他们一个复制按钮，让整个事情变得更容易。
    2.  Github 评论部分样式拖放图像——一旦上传，Markdown 图像 URL 就出现在图像被拖放的部分旁边。

    **附加上下文**在此添加关于功能请求的任何其他上下文或截图。

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1313)

*   [@torzuolih](https://dev.to/torzuolih) 注意到“最新”提要的标题被错误地标注为`top this latest`。谢谢你，雨果。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 错误页面标题带最新过滤器 #1316](https://github.com/thepracticaldev/dev.to/issues/1316) 

    [![HZooly avatar](img/e7176795c8bca932b3f56c05cd210594.png)](https://github.com/HZooly) **[HZooly](https://github.com/HZooly)** posted on [<time datetime="2018-12-12T09:33:56Z">Dec 12, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1316)

    **描述 bug**

    当您使用时间过滤器过滤标签时，HTML 标题页会更新。这适用于时间过滤器和 feed(默认过滤器)。

    选择*周*改变页面标题为: *`<tag> - top this week`* 。

    选择*提要*改变页面标题为: *`<tag>`*

    选择*最新*改变页面标题在: *`<tag> - top this latest`* // **错了？**

    **重现** [访扑](https://dev.to/t/flutter/latest)

    **预期行为**标题: *`<tag> - latest`*

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1316)

*   [@simroshka](https://dev.to/simoroshka) 提出了自动保存草稿的功能请求。理想情况下，这将通过客户端存储来完成。谢谢安娜。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)# 1318](https://github.com/thepracticaldev/dev.to/issues/1318)

    [![simoroshka avatar](img/4572dc40b7ac5a2ea1d9651e1c7e87b2.png)](https://github.com/simoroshka) **[simoroshka](https://github.com/simoroshka)** posted on [<time datetime="2018-12-12T11:44:44Z">Dec 12, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1318)

    **您的功能请求是否与某个问题相关？请描述一下。**我正在写一篇文章，需要一些关于 markdown 的帮助。我点击“帮助”按钮，阅读我需要的内容，然后按浏览器中的“后退”按钮(或关闭标签)自动返回。这个职位永远失去了。

    这似乎是一个问题，但它已经关闭:[https://github.com/thepracticaldev/dev.to/issues/42](https://github.com/thepracticaldev/dev.to/issues/42)

    **描述你想要的解决方案**我想要自动保存草稿。至少在本地存储中。

    **描述你考虑过的替代方案**另一个解决方案是询问用户是否想留下未保存的更改。在单独的标签中打开帮助信息也是一个好主意。

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1318)

*   [@davefollett](https://dev.to/davefollett) 为 CodeSandbox Liquid 标签请求了一些查询选项。请查看该问题以了解更多详情。谢谢你，戴夫。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  CodeSandbox 嵌入:添加查询字符串选项 #1321](https://github.com/thepracticaldev/dev.to/issues/1321) 

    [![davefollett avatar](img/aeaf8d919891954049375b3f8d386d1e.png)](https://github.com/davefollett) **[davefollett](https://github.com/davefollett)** posted on [<time datetime="2018-12-13T04:00:52Z">Dec 13, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1321)

    **您的功能请求是否与某个问题相关？请描述一下。** CodeSandbox.io 的共享对话框有很多选项，配置后会添加为 URL 查询字符串。Dev.to 没有提供使用它们的方法。特别是，我希望能够使用“项目初始路径”和“要显示的默认模块”选项，如下面的代码片段所示:

    [![codesandboxio-share](img/adec30003cc5e54940c087c9ec78b414.png)](https://user-images.githubusercontent.com/6683520/49914687-430c5f80-fe60-11e8-8e7a-7c5261c98537.jpg)

    这将允许我使用一个沙箱，但能够嵌入它，这样我就可以控制沙箱的显示部分。以下是屏幕片段的匹配 iframe:

    ```
    <iframe src="https://codesandbox.io/embed/24qvv1wvxr?initialpath=06%20-%20Type%20Ahead%2Findex-VUE.html&module=%2F06%20-%20Type%20Ahead%2Findex-VUE.html" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe> 
    ```

    **描述您想要的解决方案**如果 dev.to 能够公开对上述参数的控制，那就太棒了

    *   初始路径
    *   组件

    或者更好的是，提供一种方法来控制它们，作为在 id 值之后提供的单个字符串:

    `{% codesandbox ppxnl191zx QUERY-STRING %}`

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1321)

*   [@Link2Twenty](https://dev.to/link2twenty) 请求了一个 JSFiddle 液体标签/嵌入。请查看该问题以了解更多详情。谢谢你安德鲁。

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  JSFiddle 液体标签 #1323](https://github.com/thepracticaldev/dev.to/issues/1323) 

    [![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2018-12-13T15:11:45Z">Dec 13, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1323)

    我希望能够以类似于 codepen 的方式将 JSFiddle 片段嵌入到我的代码中。

    他们提供了类似这样的嵌入式解决方案

    [https://js Fiddle . net/link 2 twenty/v2 kx 9 jcd/embedded/js，html，css，result/](https://jsfiddle.net/link2twenty/v2kx9jcd/embedded/js,html,css,result/)" https://{ URL }/{ User }/{ Fiddle ID }/embedded/{ @ build _ options }/

    [![image](img/1404a9c048629a9c724ed68e5ecc24e8.png)](https://user-images.githubusercontent.com/3534427/49947525-60d1d700-fee9-11e8-81ba-202bf3a05a8d.png)

    [View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1323)