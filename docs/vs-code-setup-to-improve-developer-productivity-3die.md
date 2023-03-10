# 设置 Visual Studio 代码以提高开发人员的工作效率

> 原文：<https://dev.to/yloganathan/vs-code-setup-to-improve-developer-productivity-3die>

VS 代码最近已经成为代码编辑器的主流。我配置 VSCode 写代码，写文章，访问数据库，做代码评审，和团队沟通。目标是将从一个应用程序到另一个应用程序的移动保持在最低限度。

我在这里分享我的配置，希望能多向别人学习。

# 分机

1.  [Git lens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) -在源代码管理中选择一个文件，直接在编辑器中查看每个版本和每个版本中的更改。
2.  Docker -启动、停止直接从编辑器运行代码的容器。
3.  [Git PR](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github) -曾经想要点击你正在审查的代码吗？可能想要查看代码中对某个方法的所有引用。现在，您可以使用 git PR 在编辑器中浏览正在审查的代码。
4.  [团队聊天](https://marketplace.visualstudio.com/items?itemName=karigari.chat) -将 slack 消息带到 vscode，这样您就不必看到另一个屏幕或应用程序。
5.  [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) -直接在代码编辑器中用 Markdown 编写你的文档或文章。
6.  [粘贴图像](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image) -将图像直接粘贴到降价文件中。
7.  [PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ckolkman.vscode-postgres)-创建到 dev/uat/prod 数据库的连接，并执行任何 SQL 命令。
8.  Visual Studio intelli code——Python/JS/TS/Java 代码的自动完成。
9.  最佳多语言棉绒。
10.  代码拼写检查器 -告诉你在代码中使用有意义的单词，并且在拼写检查你写的文档时也很有用。

# [T1】settings . JSON](#settingsjson)

```
{
    "workbench.colorTheme": "Solarized Dark",
    "workbench.sideBar.location": "left",
    "workbench.settings.enableNaturalLanguageSearch": false,
    "workbench.statusBar.feedback.visible": false,
    "window.zoomLevel": 1,
    "explorer.confirmDragAndDrop": false,
    "explorer.sortOrder": "filesFirst",
    "explorer.openEditors.visible": 0,
    "breadcrumbs.enabled": true,
    "files.autoSave": "afterDelay",
    "files.insertFinalNewline": true,
    "files.trimTrailingWhitespace": true,
    "editor.suggestSelection": "first",
    "editor.minimap.enabled": false,
    "editor.cursorSmoothCaretAnimation": true,
    "editor.formatOnPaste": true,
    "editor.formatOnSave": true,
    "editor.cursorBlinking": "phase",
    "editor.smoothScrolling": true,
    "editor.renderWhitespace": "all",
    "git.autofetch": true,
    "git.postCommitCommand": "push",
    "git.alwaysShowStagedChangesResourceGroup": true,
    "gitlens.codeLens.authors.enabled": false,
        "gitlens.advanced.messages": {
        "suppressFileNotUnderSourceControlWarning": true
    },
    "gitlens.views.repositories.files.layout": "list",
    "cSpell.allowCompoundWords": true,
    "cSpell.userWords": [
        "oauth",
        "postgres",
        "repo",
        "venmo"
    ],
    "cSpell.enabledLanguageIds": [
        "markdown",
        "plaintext",
        "text"
    ]
    "telemetry.enableCrashReporter": false,
    "telemetry.enableTelemetry": false,
    "githubPullRequests.telemetry.enabled": false,
    "eslint.autoFixOnSave": true,
    "javascript.updateImportsOnFileMove.enabled": "always",
} 
```