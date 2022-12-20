# 我的最终 VSCode 配置

> 原文：<https://dev.to/vaidhyanathan93/ulitmate-vscode-configuration-4i2o>

我过去非常喜欢 vim，但是现在我转向了 VSCode，因为 UX 和插件之间的微妙平衡。

以下是我的 settings.json 文件。你也可以用同样的。

**请也添加您的配置**

禁用的小地图-因为我们正在编写模块化代码，所以没有必要这样做。

```
"editor.minimap.enabled": false 
```

**自动保存使能** -自动保存使能，因此不需要 cmd+s 总是

```
 "files.autoSave": "afterDelay",
    "files.autoSaveDelay": 5000, 
```

**排除**——这些是会让 VSCode 哭的大文件和文件夹。请在您的配置中添加相同的内容。

```
 "files.watcherExclude": {
        "**/.git/objects/**": true,
        "**/.git/subtree-cache/**": true,
        "**/node_modules/**": true,
        "**/tmp/**": true,
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/node_modules": true,
        "**/bower_components": true,
        "**/dist/**": true,
        "**/log/**": true,
        "**/logs/**": true,
        "**/.fdk/**": true
    },
    "files.exclude": {
        "**/.git/objects/**": true,
        "**/.git/subtree-cache/**": true,
        "**/node_modules/**": true,
        "**/tmp/**": true,
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/node_modules": true,
        "**/bower_components": true,
        "**/dist/**": true,
        "**/log/**": true,
        "**/.fdk/**": true
    },
    "search.exclude": {
        "**/.git/objects/**": true,
        "**/.git/subtree-cache/**": true,
        "**/node_modules/**": true,
        "**/tmp/**": true,
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/node_modules": true,
        "**/bower_components": true,
        "**/dist/**": true,
        "**/log/**": true
    }, 
```

**标签大小** -标签有两个空格，我们还是朋友。根据您的源代码对此进行更改。

```
"editor.tabSize": 2, 
```

【VSCode 的最终 Json

```
 {
    "ruby.intellisense": "rubyLocate",
    "gitlens.advanced.messages": {
        "suppressShowKeyBindingsNotice": true
    },
    "workbench.statusBar.feedback.visible": false,
    "search.location": "sidebar",
    "window.zoomLevel": 0,
    "workbench.iconTheme": "vscode-icons",
    "files.watcherExclude": {
        "**/.git/objects/**": true,
        "**/.git/subtree-cache/**": true,
        "**/node_modules/**": true,
        "**/tmp/**": true,
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/node_modules": true,
        "**/bower_components": true,
        "**/dist/**": true,
        "**/log/**": true,
        "**/logs/**": true,
        "**/.fdk/**": true
    },
    "files.exclude": {
        "**/.git/objects/**": true,
        "**/.git/subtree-cache/**": true,
        "**/node_modules/**": true,
        "**/tmp/**": true,
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/node_modules": true,
        "**/bower_components": true,
        "**/dist/**": true,
        "**/log/**": true,
        "**/.fdk/**": true
    },
    "search.exclude": {
        "**/.git/objects/**": true,
        "**/.git/subtree-cache/**": true,
        "**/node_modules/**": true,
        "**/tmp/**": true,
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/node_modules": true,
        "**/bower_components": true,
        "**/dist/**": true,
        "**/log/**": true
    },
    "editor.fontSize": 16,
    "editor.tabSize": 2,
    "emmet.includeLanguages": {
        "html": "html",
        "javascript": "javascriptreact"
    },
    "files.autoSave": "afterDelay",
    "files.autoSaveDelay": 5000,
    "terminal.external.osxExec": "iTerm.app",
    "terminal.integrated.shell.osx": "zsh",
    "editor.wordWrap": "on",
    "editor.formatOnSave": true,
    "editor.formatOnPaste": true,
    "editor.minimap.enabled": false,
    "editor.fontLigatures": true,
    "workbench.editor.highlightModifiedTabs": true,
    "explorer.sortOrder": "modified",
    "files.trimFinalNewlines": true
} 
```