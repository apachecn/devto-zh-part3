# git-历史

> 原文：<https://dev.to/nickytonline/git-history-20nh>

大声呼喊[乔希·科莫](https://twitter.com/JoshWComeau)为我指明了回购的方向。他把它放在了我的推特上。

对于喜欢漂亮视觉效果的人来说，这是查看 git 历史的一种非常酷的方式。滑动历史，看到以前的变化优雅地滑入。

> ![unknown tweet media content](img/361082d94c672e77686c8667c0740a07.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/DzJSwqGWsAIIXGQ.mp4" type="video/mp4"></video>![Rodrigo Pombo profile image](img/99e571845e5832260853f0a7c9297b9f.png)Rodrigo Pombo[@ pomber](https://dev.to/pomber)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我为 git 历史做了一个 CLI，它与任何本地 Git 回购都有效。
> 试试看:“npx git-file-history your/file . foo”。
> 
> 回购来了(5k⭐️已经！)[github.com/pomber/git-his…](https://t.co/3benAi3rTP)2019 年 2 月 11 日下午 18:38[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1095029354839588867)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1095029354839588867)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1095029354839588867)

"快速浏览任何 GitHub 的历史(GitLab 和 Bitbucket 即将推出)"

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [庞贝](https://github.com/pomber) / [吉特-历史](https://github.com/pomber/git-history)

### 从任何 git 存储库中快速浏览文件的历史

<article class="markdown-body entry-content container-lg" itemprop="text">[![demo](img/5a3bcb98170197b7c4efa2acfaeb3704.png)](https://github.githistory.xyz/torvalds/linux/blob/master/kernel/up.c) 

# [Git 历史记录](https://githistory.xyz)

快速浏览任何 git 存储库中文件的历史记录:

1.  转到 **GitHub** (或 **GitLab** ，或 **Bitbucket** 中的一个文件
2.  将`github.com`替换为`github.githistory.xyz`
3.  没有第三步

[试试看](https://github.githistory.xyz/babel/babel/blob/master/packages/babel-core/test/browserify.js)

> 如果你喜欢这个项目，考虑支持我在 Patreon 上的开源工作！
> 并在推特上关注 [@pomber](https://twitter.com/pomber) 的更新。

## 扩展ˌ扩张

### 浏览器

你也可以用 [Chrome](https://chrome.google.com/webstore/detail/github-history-browser-ex/laghnmifffncfonaoffcndocllegejnf) 和 [Firefox](https://addons.mozilla.org/firefox/addon/github-history/) 扩展在 GitHub、GitLab 和 Bitbucket 上添加一个`Open in Git History`按钮。

<details><summary>Or you can use a bookmarklet.</summary>

```
javascript: (function() {
  var url = window.location.href;
  var regEx = /^(https?\:\/\/)(www\.)?(github|gitlab|bitbucket)\.(com|org)\/(.*)$/i;
  if (regEx.test(url)) {
    url = url.
```

…Enter fullscreen mode Exit fullscreen mode</details></article>

[View on GitHub](https://github.com/pomber/git-history)

感谢有趣的项目 [@pomber](https://dev.to/pomber) ！

[内森·安德森](https://unsplash.com/photos/c7fShzYk4bA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照