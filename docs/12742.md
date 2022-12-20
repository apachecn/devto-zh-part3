# 无需离开命令行即可合并 GitHub Pull 请求

> 原文：<https://dev.to/acro5piano/merge-github-pull-requests-from-command-line-in-fish-shell-2ild>

我之前在个人项目中的工作流程是这样的:

*   `git checkout -b awesome-feature`
*   编辑文件
*   `git add .; git commit -m 'created awesome feature'`
*   `git push origin awesome-feature`
*   `hub pull-request`使用[中枢命令](https://hub.github.com/hub.1.html)
*   打开 GitHub 公关页面
*   按下“合并按钮”

虽然我在 Git 中使用了一些别名，但是`Open GitHub PR page`和`Press the "Merge Button"`是压力很大任务。我不能忍受浏览器加载！

基本上，我不需要为我的个人项目审查我自己的拉请求，所以我创建了一个微小的函数来合并来自命令行的拉请求:

```
function merge
    set repo (pwd | perl -pe 's#.+github.com/##')

    curl \
        -XPUT \
        -H "Authorization: token $GITHUB_TOKEN" \
        https://api.github.com/repos/$repo/pulls/$argv[1]/merge
end 
```

Enter fullscreen mode Exit fullscreen mode

[更多细节线](https://github.com//acro5piano/dotfiles/blob/f42d4b1c10aab1fff0dc139f0be09b58a22cd820/.config/fish/config.fish#L235)

像这样使用命令:

```
merge 22 
```

Enter fullscreen mode Exit fullscreen mode

很简单。

它假设你在`/path/to/github.com/user/repo`。您还可以使用`git remote -v`来获取当前目录的存储库信息。

我将我的点文件发布到 GitHub，所以我创建了`GITHUB_TOKEN`环境变量，这是非常敏感的信息。

[https://github.com/acro5piano/dotfiles](https://github.com/acro5piano/dotfiles)