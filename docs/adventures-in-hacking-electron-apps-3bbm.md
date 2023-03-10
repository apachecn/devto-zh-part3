# 黑客入侵电子应用的冒险

> 原文：<https://dev.to/essentialrandom/adventures-in-hacking-electron-apps-3bbm>

*免责声明:黑客攻击应用程序通常违反服务条款。这篇文章纯粹是理论性的，并不是对实践的认可。永远负责任地黑。*

你有没有被某个 app 或者网站的某个方面*极度烦*？

如果你和我一样，答案可能是肯定的。如果你很像我，答案是“是的，经常。”

在网络上，我可以很容易地解决大多数不满，这要感谢允许你在任何网站上注入定制 CSS 和 Javascript 的[扩展。然而，对于桌面应用程序，我通常不得不忍受痛苦，希望开发人员有一天会明白过来并决定解决问题(或者最终有时间优先考虑这样做)。](https://chrome.google.com/webstore/detail/user-javascript-and-css/nbhcbdghjpllgmfilhnhkllmkecfmpld)

除非 app 是[电子](https://electronjs.org/) app。

我不记得我是如何了解到这种可能性的——有时候，没有比修正糟糕设计的需求更强的驱动力了。

在这篇文章中，我将谈论我如何改变 Discord 的应用程序代码来解决我([和](https://support.discordapp.com/hc/en-us/community/posts/360037394592-Reduce-horizontal-size-of-window-or-pop-out-chats) [其他的](https://support.discordapp.com/hc/en-us/community/posts/360029464911-Make-minimum-window-size-an-optional-feature-))最大的不满之一:它巨大的最小窗口大小。

[![Discord's default minimum window size](img/fcc320a62ae6cc3156b1619c755b1fb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w9Ve6uYT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l4j1d4o62f83tezw24im.png)

<figcaption>Discord's *huge* minimum window size (940px).</figcaption>

不过，让我们明确一点:这篇文章——就像任何有价值的软件努力一样——是关于*旅程*而不是解决方案。所以，跟随我的冒险，看看我是如何计算出需要的步骤，并了解一些关于命令行，电子应用程序和黑客！

*注意:当我使用 macOS High Sierra 时，这个过程可以在其他操作系统上类似地复制。*

# 揭开不和的秘密

最初，我想尽可能多地了解 Discord 应用程序。MacOS 有一个 [UI 机制来探索应用程序内容](https://apple.stackexchange.com/questions/15658/how-can-i-open-a-pkg-file-manually/15665#15665)，这是一个很好的“零步”。然而，手动探索只能到此为止，很快我转向了命令行。

## 第一步:Discord 的流程是什么样子的？

为了回答我自己的问题，我运行了以下命令:

```
ps x | grep Discord 
```

简而言之，`ps`列出了所有正在运行的进程，`x`包括那些没有连接到 shell 的进程(例如，那些通过点击应用程序图标来启动的进程)，并且将这个输出(`|`)通过管道传输到`grep`命令只显示那些带有字符串`Discord`的进程。你可以在 explainshell.com[了解更多信息。](https://explainshell.com/explain?cmd=ps+x+%7C+grep+Discord)

以下是为可读性而编辑的输出:

```
1927   ??  S      0:00.08 /Applications/Discord.app/Contents/Frameworks/Electron Framework.framework/Resources/crashpad_handler 
    --no-rate-limit --no-upload-gzip 
    --database=/var/folders/sm/4v5p46v175d3x94qp56r37340000gn/T/Discord Crashes 
    --metrics-dir=/var/folders/sm/4v5p46v175d3x94qp56r37340000gn/T/Discord Crashes 
    --url=http://crash.discordapp.com:1127/post 
    --handshake-fd=73 
```

<figcaption>Process #1927</figcaption>

```
1928   ??  R     34:58.78 /Applications/Discord.app/Contents/Frameworks/Discord Helper.app/Contents/MacOS/Discord Helper 
    --type=renderer --no-sandbox --autoplay-policy=no-user-gesture-required 
    --force-color-profile=srgb --enable-features=SharedArrayBuffer 
    --disable-features=MacV2Sandbox --service-pipe-token=5494336596696404231 
    --lang=en-US 
    --app-path=/Applications/Discord.app/Contents/Resources/app.asar 
    --node-integration=false --webview-tag=false --no-sandbox 
    --preload=/Users/essential_randomness/Library/Application Support/discord/0.0.254/modules/discord_desktop_core/core.asar/app/mainScreenPreload.js 
    --background-color=#2f3136 --num-raster-threads=2 --enable-zero-copy 
    --enable-gpu-memory-buffer-compositor-resources 
    --enable-main-frame-before-activation 
    --service-request-channel-token=5494336596696404231 --renderer-client-id=6 
```

<figcaption>Process #1928</figcaption>

第一个过程(#1927)似乎与报告应用程序崩溃有关。我这样假设是因为应用程序路径中的`.../crashpad_handler`，以及指向`http://crash.discordapp.com:1127/post`的`url`标志(这可能是服务器端点崩溃报告被传递到的地方)。

第二个过程(#1928)更有希望。特别是，我发现`app-path`变量(`/Applications/Discord.app/Contents/Resources/app.asar`)的值值得探究。

## 第二步:提取 App 代码。

文件的扩展名引起了我的兴趣。在谷歌上快速搜索“asar 文件”后，我找到了一个解释格式的 [GitHub repo](https://github.com/electron/asar) :

> Asar 是一种简单的扩展存档格式，它像 tar 一样将所有文件连接在一起，无需压缩，同时支持随机访问。

幸运的是，这个存储库包含了如何安装 asar 命令行实用程序(`npm install asar`)以及如何从 asar 文件中提取归档文件的信息。我的下一步既明显又简单。

然而，在进行任何更改之前，我决定备份原始归档:

```
# Backup original file in case of emergency
cd /Applications/Discord.app/Contents/Resources/
cp app.asar app_safe_copy.asar

# Extract app.asar to a folder named "unpacked"
asar extract app.asar unpacked/ 
```

打开这个隐喻的百宝箱后，是时候列出(`ls`)它的内容了！

```
cd unpacked
ls
# output:
> app_bootstrap common        node_modules  package.json

ls app_bootstrap/
# output:
> Constants.js       bootstrap.js       ...       appSettings.js       ... 
```

熟悉的`node_modules/` + `package.json`组合指向归档是一个 npm 包，将一堆 JavaScript 文件和图像等其他东西捆绑在一起。这显然是重要的代码！更好的是，代码没有以任何方式编译或加密。

我第一次认为(也许)我真的可以完成这件事！

## 第三步:这是什么代码？

我通过在 VsCode 中打开 JS 文件来研究它们，以便对应用程序的结构有所了解。这很有趣，但是非常慢。

为了更快，我决定赌一个简单的假设:任何控制窗口宽度的文件都必须包含字符串“width”本身！

另外，我可以从我的搜索中排除 node_modules 文件夹，因为 npm 包为外部库保留了这个目录。

```
# Find all the files containing the string width in the current folder,
# but exclude the ones in the node_modules one.
grep -iRl "width" ./ | grep -v node_modules
# Output:
> .//app_bootstrap/splash/index.js
> .//app_bootstrap/splash/variables.json
> .//app_bootstrap/splashScreen.js 
```

[对命令的深入解释。](https://explainshell.com/explain?cmd=grep+-iRl+%22width%22+.%2F+%7C+grep+-v+node_modules)

这个输出令人失望:文件明显与闪屏相关，这不是我想要改变的。我尝试进入顶层的 Discord 文件夹(`/Applications/Discord.app/`)并再次运行该命令，但是输出并没有太大的不同。

看来我的运气已经用完了。

## 第四步:有时候后退就是前进一步

与其绝望，我决定回到流程#1928。`preload`旗代表另一条有趣的路径，位置与前一条完全不同:`/Users/essential_randomness/Library/Application Support/discord/0.0.254/modules/discord_desktop_core/core.asar/app/mainScreenPreload.js`。

是时候开始另一次冒险了！

```
# Once again, I searched for files containing the string "width".
cd /Users/essential_randomness/Library/Application\ Support/discord/
grep -iRl "width" ./ | grep -v node_modules
# Output
> .//Preferences
> ...
> .//settings.json
> ...
> .//0.0.254/modules/discord_desktop_core/core.asar 
```

<figcaption>This was when I found out you can grep the content of asar files without unpacking them first. Forgetting steps can lead to interesting discoveries.</figcaption>

这次搜索得到了相当多的文件，所以我决定进一步缩小搜索范围。因为我想改变最小宽度，所以我认为任何相关的变量名都应该叫做 minWidth 或 min_width。毕竟，我们都很重视代码的可读性，不是吗？

```
grep -iRl "min_width" ./ | grep -v node_modules
# Output
> .//0.0.254/modules/discord_desktop_core/core.asar

grep -iRl "minWidth" ./ | grep -v node_modules
# Output
> .//0.0.254/modules/discord_desktop_core/core.asar
> .//0.0.254/modules/discord_voice/discord_voice.node 
```

看起来很有前途！我再次提取它并搜索正确的文件:

```
cd 0.0.254/modules/discord_desktop_core
cp core.asar core_safe_copy.asar
asar extract core.asar core_unpacked
cd core_unpacked

# Trying min_width first, as the value is likely a constant.
# Constants use, by many code conventions, a capitalized style (i.e. "MIN_WIDTH").
grep -iRl "min_width" ./ | grep -v node_modules
# Output:
> .//app/mainScreen.js 
```

最终会是那个人吗？我立即打开它，搜索“最小宽度”...

```
const MIN_WIDTH = settings.get('MIN_WIDTH', 940);
const MIN_HEIGHT = settings.get('MIN_HEIGHT', 500); 
```

<figcaption>...JACKPOT!</figcaption>

## 第五步:真相时刻

我知道我必须有所发现。我不知道这是否可行(但充满信心)，于是编辑了代码:

```
 const MIN_WIDTH = settings.get('MIN_WIDTH', 0);
const MIN_HEIGHT = settings.get('MIN_HEIGHT', 0); 
```

现在我需要做的就是重新打包修改后的 asar 文件。同样，在继续之前，我确保创建了 core.asar ( `cp core.asar core_safe_copy.asar`)的备份。在这里完全杜绝不和谐是一种真正的可能性！

我战战兢兢地跑完了最后一步:

```
# Remove the original app file and swap it with our edited code, repacked.
rm core.asar
asar pack core_unpacked core.asar 
```

此时，我重启了 Discord，希望这些改变能够生效。我把光标放在应用程序的边框上，开始拖动...成功了！

[![Discord with minimum window size removed](img/c59341b07b73618c70076894e62fce5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--po2_-Tin--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v0ejjedxsokhcy7c4eru.gif)

<figcaption>Discord with minimum window size removed</figcaption>

### 支线任务:“我也喜欢危险地活着”

此时，我还有最后一个好奇心。如果我在修改代码时搞砸了，会发生什么？

我重新提取了 asar 文件，故意插入了一个无效的 JavaScript 语句，重新打包，并试图运行这个应用程序。不出所料，我出错了！

这削弱了备份的重要性。因为我明智地创建了 core.asar 的副本，所以我简单地将原始代码放回原处，错误就消失了。

# 结论

作为最后的警告，以这种方式修改代码很可能违反大多数服务条款(提示通常的“强大的力量= >巨大的责任”演讲)。

代码入侵要考虑的另一个方面是副作用:Discord 没有优化为以较小的尺寸显示，UI 可能会不稳定。由于电子应用程序使用 Chromium 作为前端，我通过开发者工具控制台(Discord 友好地在“视图>开发者>开发者工具”下提供)自己修改了 UI。

请记住，开发人员不希望他们的代码被修改的一个非常有效的原因是，这可能会导致应用程序本身出现意外的错误。如果你选择运行任何代码的定制版本，除非你能在原始应用程序中重现错误，否则不要提交错误！

# 一个滑稽的尾声

在经历了这一切之后，我发现有一种更简单的方法来改变 Discord 的窗口大小，不需要修改源代码。

但是，你知道，那有什么意思呢？

黑客快乐！