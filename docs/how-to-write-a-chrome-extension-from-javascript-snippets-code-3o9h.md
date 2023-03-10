# 如何从 JavaScript 代码片段编写 Chrome 扩展

> 原文：<https://dev.to/eviltester/how-to-write-a-chrome-extension-from-javascript-snippets-code-3o9h>

编写一个 Chrome 扩展相当容易(在 Chrome 商店获得它要困难得多！).在这篇文章中，我将使用我编写的代码片段来生成 CounterStrings 并将其转换成 Chrome 扩展。

我在帖子底部有一段视频展示了整个过程。正文基本上是一个总结。

你可以在 Github 上找到这个 [Chrome 扩展的代码](https://github.com/eviltester/counterstringjs)

## 反串汇总

Counterstring 是一个类似于`*3*5*7*9*12*15*`的字符串，其中`*`表示紧接在它前面的数字在字符串中的位置。

*   [反字符串算法](https://www.eviltester.com/2018/05/counterstring-algorithms.html)
*   詹姆斯·巴赫在 satisfice.com 的原创作品和复弦作品
*   [上一篇在 JavaScript 中实现计数器字符串的文章](https://www.eviltester.com/blog/eviltester/2019-02-19-counterstring-snippets/)

## 一份清单

首先我们需要创建一个`manifest.json`文件。

*   [developer.chrome.com/extensions/manifest](https://developer.chrome.com/extensions/manifest)

这将声明扩展的名称和版本，并链接到正在使用的脚本。它也是您声明所需权限的地方。

当您编写扩展时，您会看到您正在使用哪些命令，这些命令将具有开发人员文档中所需的权限。

从 Chrome 教程中记录的基本清单开始，并在添加代码时构建它。

我从……开始

~~~~ ~~、
{
、【manifest _ version】:2、
、【name】:“Counterstring”、
、【version】:“0.1”、
、【description】:“简单的 Counterstring 生成”
}
~ ~ ~~~

等我写完的时候，已经变成了:

~ ~ ~ ~ ~~{
【manifest _ version】:2、
【name】:" Counterstring "、
【version】:" 0.1 "、
【description】:"简单的 Counterstring 生成"、
【background】:{
【persistent】:true、
【scripts】:[" js/background . js "]
}、
【permissions】:[
【context menus】、~~ 

## [T1】background . js](#backgroundjs)

`background.js`是您可能构建大部分扩展的地方。

我想创建一个我能想到的最简单的扩展，我认为拥有一个右键菜单是最简单的。这就是我的扩展的全部功能。

我使用了 contenxt 菜单的文档:

*   [developer.chrome.com/extensions/contextMenus](https://developer.chrome.com/extensions/contextMenus)

这告诉我添加了`"contextMenus"`权限。我还定义了上下文菜单，只在我右击可编辑的 web 元素`"contexts" : ["editable"]`时出现。

因为我的 CounterString 将被添加到 web 元素中。

~ ~ ~ ~ ~~context menus . create CounterString =
chrome . context menus . create(
{ " title ":"生成 Counterstring "，
" contexts ":[" editable "]
}，
function(){
if(chrome . runtime . lasterror){
console . error(chrome . runtime . lasterror . message)；
}
}
)；
~ ~ ~~~

为了处理上下文菜单上的点击，我添加了一个监听器。

~ ~ ~ ~~~t1]chrome . context menus . onclicked . addlistener(context menu handler)；
~ ~ ~~~

然后处理上下文菜单点击的函数使用`chrome.tabs.executeScript`将一些 JavaScript 注入到活动选项卡中。

*   [关于执行脚本](https://developer.chrome.com/extensions/tabs#method-executeScript)

~~~~ ~~函数 contextMenuHandler(info，tab){~~

```
if(info.menuItemId===contextMenus.createCounterString){
    chrome.tabs.executeScript({
        file: 'js/counterstring.js'
      });
} 
```

Enter fullscreen mode Exit fullscreen mode

}
~ ~ ~

## counterstring.js

js 正是我作为[片段](https://www.eviltester.com/blog/eviltester/2019-02-19-counterstring-snippets/)编写的代码，但是我把它保存为`counterstring.js`文件

~~~~ ~~函数 reverseString(reverseMe){
返回 reverseMe.split(" ")。反转()。join(" ")；
}~~

函数 getCounterString(count){

```
var counterString = "";

while(count>0){

    var appendThis = "*" + reverseString(count.toString());

    if(appendThis.length>count){
        appendThis = appendThis.substring(0,count);
    }    

    counterString = counterString + appendThis;

    count = count - appendThis.length;
}

return reverseString(counterString); 
```

Enter fullscreen mode Exit fullscreen mode

}

var count = window.prompt("反字符串长度？", "100");
var counterString = getCounterString(计数)；
console . log(counterString)；
document . active element . value = counter string；
~ ~ ~

## 持久的背景

为了注入代码，我需要使 Background.js 持久化。

## 图标

Chrome 希望扩展有如下描述的图标:

*   [developer.chrome.com/extensions/manifest/icons](https://developer.chrome.com/extensions/manifest/icons)

当你写你的扩展的时候，你实际上不需要这么做，这很有帮助，但是只有当你想发布到 Chrome 商店的时候你才真正需要它。

## 从代码中运行您的扩展

为了运行您的扩展，您必须:

*   访问 chrome://extensions
*   打开开发者模式
*   单击加载解压缩
*   选择扩展文件夹(包含 manifest.json 的文件夹)

## 视频

我制作了一个视频来展示这个过程。

在视频中，您将看到完整的过程以及出现错误时如何调试扩展:

*   如何将现有的 javascript 代码转换成 Chrome 扩展
*   扩展的基本结构，manifest.json，后台脚本和内容脚本
*   扩展中的权限:上下文菜单，活动选项卡
*   如何为扩展创建上下文菜单
*   如何更改上下文菜单的显示位置:上下文，可编辑
*   为什么要使用持久的后台脚本
*   如何使用 executescript 将内容脚本动态注入页面
*   如何在上下文菜单中使用监听器

[https://www.youtube.com/embed/Olz4wo-ILwI](https://www.youtube.com/embed/Olz4wo-ILwI)

## 代码

你可以在 Github 上找到源代码。

*   [github.com/eviltester/counterstringjs](https://github.com/eviltester/counterstringjs)

最初发布于[Eviltester.com](https://www.eviltester.com/blog/eviltester/2019-02-20-counterstring-extension/)