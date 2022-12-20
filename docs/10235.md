# 像在 1999 年一样浏览您的 vs code(vim 方式)

> 原文：<https://dev.to/karlredman/navigate-your-vscode-like-its-1999-the-vim-way-3632>

如果你是一个 vim 用户，在没有 vim 击键的情况下使用 vscode 可能会令人沮丧。奇妙的插件 [vscodevim](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim) 只能到此为止。如果你和我一样，习惯了`M-h,M-j,M-k,M-l`(其中`M` == 'Alt 键')和 vim 式的 tab 移动，事情会变得很疯狂。请注意，默认 vim 中的这些键与按下`escape`是一样的【我讨厌不得不按下`esc`键——永远不要】。

这个配置演示了如何从 vim 轻松切换到 vscode。此外，还提供了一种类似 vim 的切换选项卡的方法。最后，这些设置允许您使用书签导航`vscode`，就像在常规 vim 中使用`marks`一样。

等等，还有更多！通读下面的代码，找到许多类似 vim 的 JEM，它们会让 vscode 感觉离家更近一点。

*   测试对象:
    *   vscode v1.32.1
    *   vim 8.1 [如何编译](https://dev.to/karlredman/compile-and-install-vim-v81-from-source-with-pyenv-5cjc)
    *   MX Linux 18.1
    *   Debian Stretch 9.8

## Vim 配置(由您决定)

### 在 vcode 中打开您当前的 vim 文档

*   将下面这一行添加到您的`~/.vimrc`
*   这(默认)意味着`\ov`将从 vim 的 vscode 中打开当前文件

```
nnoremap <leader>ov :exe ':silent !code %'<CR>:redraw!<CR> 
```

### 使用`ctrl+j`和`ctrl+k`来改变 vim 中的标签(我更喜欢根据需要向上/向下调整)

```
" switch tabs (same as gt & gT)
nnoremap <C-j> :tabprevious<CR>
nnoremap <C-k> :tabnext<CR>
"
" (bonus) move tabls right or left
map <C-h> :execute "tabmove" tabpagenr() - 2 <CR>
map <C-l> :execute "tabmove" tabpagenr() + 1 <CR> 
```

## VS 代码变更

这很难解释。我将在这里列出函数和例子，然后根据需要编辑代码。我必须配置`keybindings.json`和`settings.json`——我还没有尝试组合配置。

*   安装插件 [Vim - Visual Studio 市场](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)
*   安装插件[编号书签- Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=alefragnani.numbered-bookmarks)
*   配置:`~/.config/Code/User/keybindings.json`
*   配置:`~/.config/Code/User/settings.json`

### 功能来自`keybindings.json`(+notes settings . JSON)

*   焦点活动编辑器(vim 窗口)

    *   即(类似于 vim `C-w`快捷键)

```
 {  "key":  "ctrl+w j",  "command":  "workbench.action.focusBelowGroup",  }, 
```

*   焦点窗口组

    *   (类似于 tmux 窗格)

```
{  "key":  "ctrl+w down",  "command":  "workbench.action.moveActiveEditorGroupDown",  }, 
```

*   对`<ALT>` + `h`，`j`，`k`，`l`无所事事

    *   务必禁用 vscode 设置中的菜单键:`Uncheck`“设置- >“窗口- >“启用菜单栏 Mneumonics”
    *   与默认 vim 行为相同

```
 {  "key":  "alt+h",  "command":  "extension.vim_escape",  "when":  "editorTextFocus && vim.active && vim.mode == 'Insert'"  }, 
```

*   创建`mark`

    *   参见文件(`/.conf/Code/User/settings.json`)

```
 "vim.normalModeKeyBindingsNonRecursive":  [  {  "before":  ["m","a"],  "commands":  [  "numberedBookmarks.toggleBookmark1"  ]  }  ] 
```

*   跳到`mark`

    *   即跳到`mark a`

```
{  "key":  "' a",  "command":  "numberedBookmarks.jumpToBookmark1",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  }, 
```

## 文件/配置

至此，我想你已经明白了。我已经在配置文件中添加了其他几个类似 vim 的设置，但是像 Vim 一样，您必须阅读代码才能找到精华。

*   `~/.config/Code/User/keybindings.json`

```
{  "vim.easymotion":  true,  "vim.sneak":  true,  "vim.incsearch":  true,  "vim.useSystemClipboard":  true,  "vim.useCtrlKeys":  true,  "vim.hlsearch":  true,  "vim.leader":  "\\",  "vim.handleKeys":  {  "<C-a>":  false,  "<C-f>":  false  },  "vim.normalModeKeyBindingsNonRecursive":  [  {  "before":  ["<C-n>"],  "commands":  [":nohl"]  },  {  "before":  ["m","a"],  "commands":  [  "numberedBookmarks.toggleBookmark1"  ]  },  {  "before":  ["m","b"],  "commands":  [  "numberedBookmarks.toggleBookmark2"  ]  },  {  "before":  ["m","c"],  "commands":  [  "numberedBookmarks.toggleBookmark3"  ]  },  {  "before":  ["m","d"],  "commands":  [  "numberedBookmarks.toggleBookmark4"  ]  },  {  "before":  ["m","e"],  "commands":  [  "numberedBookmarks.toggleBookmark5"  ]  },  {  "before":  ["m","f"],  "commands":  [  "numberedBookmarks.toggleBookmark6"  ]  },  {  "before":  ["m","g"],  "commands":  [  "numberedBookmarks.toggleBookmark7"  ]  },  {  "before":  ["m","h"],  "commands":  [  "numberedBookmarks.toggleBookmark8"  ]  },  {  "before":  ["m","i"],  "commands":  [  "numberedBookmarks.toggleBookmark9"  ]  },  {  "before":  ["m","j"],  "commands":  [  "numberedBookmarks.toggleBookmark0"  ]  },  ],  "breadcrumbs.enabled":  true,  "workbench.activityBar.visible":  true,  "workbench.statusBar.visible":  true,  "workbench.sideBar.location":  "left",  "zenMode.hideStatusBar":  false,  "zenMode.hideTabs":  false,  "window.enableMenuBarMnemonics":  false,  "window.menuBarVisibility":  "default",  "window.zoomLevel":  1,  "telemetry.enableTelemetry":  false,  "telemetry.enableCrashReporter":  false  } 
```

*   `~/.config/Code/User/settings.json`

```
//  Place  your  key  bindings  in  this  file  to  override  the  defaults  [  {  "key":  "ctrl+w j",  "command":  "workbench.action.focusBelowGroup",  },  {  "key":  "ctrl+w k",  "command":  "workbench.action.focusAboveGroup",  },  {  "key":  "ctrl+w h",  "command":  "workbench.action.focusLeftGroup",  },  {  "key":  "ctrl+w l",  "command":  "workbench.action.focusRightGroup",  },  {  "key":  "ctrl+w down",  "command":  "workbench.action.moveActiveEditorGroupDown",  },  {  "key":  "ctrl+w up",  "command":  "workbench.action.moveActiveEditorGroupUp",  },  {  "key":  "ctrl+w left",  "command":  "workbench.action.moveActiveEditorGroupLeft",  },  {  "key":  "ctrl+w right",  "command":  "workbench.action.moveActiveEditorGroupRight",  },  {  "key":  "alt+h",  "command":  "extension.vim_escape",  "when":  "editorTextFocus && vim.active && vim.mode == 'Insert'"  },  {  "key":  "alt+j",  "command":  "extension.vim_escape",  "when":  "editorTextFocus && vim.active && vim.mode == 'Insert'"  },  {  "key":  "alt+k",  "command":  "extension.vim_escape",  "when":  "editorTextFocus && vim.active && vim.mode == 'Insert'"  },  {  "key":  "alt+l",  "command":  "extension.vim_escape",  "when":  "editorTextFocus && vim.active && vim.mode == 'Insert'"  },  {  "key":  "' a",  "command":  "numberedBookmarks.jumpToBookmark1",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' b",  "command":  "numberedBookmarks.jumpToBookmark2",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' c",  "command":  "numberedBookmarks.jumpToBookmark3",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' d",  "command":  "numberedBookmarks.jumpToBookmark4",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' e",  "command":  "numberedBookmarks.jumpToBookmark5",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' f",  "command":  "numberedBookmarks.jumpToBookmark6",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' g",  "command":  "numberedBookmarks.jumpToBookmark7",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' h",  "command":  "numberedBookmarks.jumpToBookmark8",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' i",  "command":  "numberedBookmarks.jumpToBookmark9",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "' j",  "command":  "numberedBookmarks.jumpToBookmark0",  "when":  "editorTextFocus && vim.active && vim.mode == 'Normal'"  },  {  "key":  "ctrl+1",  "command":  "-numberedBookmarks.jumpToBookmark1",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+2",  "command":  "-numberedBookmarks.jumpToBookmark2",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+3",  "command":  "-numberedBookmarks.jumpToBookmark3",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+4",  "command":  "-numberedBookmarks.jumpToBookmark4",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+5",  "command":  "-numberedBookmarks.jumpToBookmark5",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+6",  "command":  "-numberedBookmarks.jumpToBookmark6",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+7",  "command":  "-numberedBookmarks.jumpToBookmark7",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+8",  "command":  "-numberedBookmarks.jumpToBookmark8",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+9",  "command":  "-numberedBookmarks.jumpToBookmark9",  "when":  "editorTextFocus"  },  {  "key":  "ctrl+shift+tab",  "command":  "workbench.action.focusNextGroup"  },  {  "key":  "ctrl+j",  "command":  "workbench.action.previousEditorInGroup"  },  {  "key":  "ctrl+pagedown",  "command":  "workbench.action.nextEditorInGroup"  },  {  "key":  "ctrl+w shift+-",  "command":  "workbench.action.minimizeOtherEditors"  },  {  "key":  "ctrl+w =",  "command":  "workbench.action.evenEditorWidths"  },  {  "key":  "ctrl+w v",  "command":  "workbench.action.splitEditorRight"  },  {  "key":  "ctrl+w s",  "command":  "workbench.action.splitEditorDown"  },  {  "key":  "ctrl+w c",  "command":  "workbench.action.closeActiveEditor"  },  {  "key":  "ctrl+w shift+k",  "command":  "workbench.action.moveEditorToAboveGroup"  },  {  "key":  "ctrl+w shift+j",  "command":  "workbench.action.moveEditorToBelowGroup"  },  {  "key":  "ctrl+w shift+h",  "command":  "workbench.action.moveEditorToLeftGroup"  },  {  "key":  "ctrl+w shift+l",  "command":  "workbench.action.moveEditorToRightGroup"  },  {  "key":  "ctrl+b",  "command":  "-extension.vim_ctrl+b",  "when":  "editorTextFocus && vim.active && vim.use<C-b> && !inDebugRepl && vim.mode != 'Insert'"  },  {  "key":  "ctrl+k",  "command":  "-extension.vim_ctrl+k",  "when":  "editorTextFocus && vim.active && vim.use<C-k> && !inDebugRepl"  },  {  "key":  "ctrl+b b",  "command":  "workbench.action.toggleSidebarVisibility"  },  {  "key":  "ctrl+b",  "command":  "-workbench.action.toggleSidebarVisibility"  },  {  "key":  "ctrl+b a",  "command":  "workbench.action.toggleActivityBarVisibility"  },  {  "key":  "ctrl+b f",  "command":  "workbench.files.action.showActiveFileInExplorer"  },  {  "key":  "ctrl+b n",  "command":  "workbench.action.nextSideBarView"  },  {  "key":  "ctrl+b p",  "command":  "workbench.action.previousSideBarView"  },  {  "key":  "ctrl+b m",  "command":  "workbench.action.toggleMenuBar"  },  {  "key":  "ctrl+b s",  "command":  "workbench.action.toggleStatusbarVisibility"  },  {  "key":  "ctrl+b l",  "command":  "workbench.action.toggleSidebarPosition"  },  {  "key":  "ctrl+b o",  "command":  "outline.focus"  },  {  "key":  "ctrl+b e",  "command":  "workbench.files.action.focusFilesExplorer"  },  {  "key":  "ctrl+b r",  "command":  "search.action.focusSearchList"  },  {  "key":  "ctrl+' l",  "command":  "workbench.action.togglePanelPosition"  },  {  "key":  "ctrl+' '",  "command":  "workbench.action.togglePanel"  },  {  "key":  "ctrl+j",  "command":  "-workbench.action.togglePanel"  },  {  "key":  "ctrl+' c",  "command":  "workbench.debug.panel.action.clearReplAction",  "when":  "inDebugRepl"  },  {  "key":  "meta+l",  "command":  "-workbench.debug.panel.action.clearReplAction",  "when":  "inDebugRepl"  },  {  "key":  "ctrl+' c",  "command":  "workbench.action.closePanel"  },  {  "key":  "ctrl+' space",  "command":  "workbench.action.focusPanel"  },  {  "key":  "ctrl+' n",  "command":  "workbench.action.nextPanelView"  },  {  "key":  "ctrl+' p",  "command":  "workbench.action.previousPanelView"  },  {  "key":  "ctrl+' shift+-",  "command":  "workbench.action.toggleMaximizedPanel"  }  ] 
```