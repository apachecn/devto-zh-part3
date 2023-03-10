# vim 的橡胶主题

> 原文：<https://dev.to/vonheikemen/rubber-themes-for-vim-gj5>

一组配色方案(目前只有两个)为 Vim 提供了最少的高亮显示。

## 灵感

灵感来源于[雪花石膏配色方案](https://github.com/tonsky/sublime-scheme-alabaster)的相同原则，用于美化文字:

> *   Do not highlight standard language keywords (if, else, function, etc.). They are usually the most obvious parts of any program.
>     
>     
> *   Highlight comments. Most schemes try to dilute comments by using low contrast gray. If the code is complex enough to deserve an explanation, then it is that explanation, and then we should see and read it first. It's a crime to hide.
>     
>     
> *   No font change is used. It is difficult to scan the code when it keeps jumping between normal, bold and italic. In addition, not all fonts are available in bold/italic variants.
>     
>     
> *   The fewest rules mean that you can consciously use them to find the exact information you need. You can easily remember all the rules. For example, if you need to look for a string, you know you are looking for a green token. And all the strings really pop out, because not much else stands out.
>     
>     
> *   Only the contents that the parser can reliably identify are highlighted. When it is unreliable, your brain will spend precious brain cycles to re-check everything it sees on the screen.

## 看起来怎么样

### 橡胶

VSCode 的 [rubber-theme](https://github.com/apust/vscode-rubber-theme) 的一个端口。

[![preview rubber](img/f20a98ac1593931e8d55ab2be2bc18e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k3pMzX8r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/VonHeikemen/rubber-themes.vim/master/assets/screenshot-rubber.png)

### 橡胶猫头鹰

同样的事情，但使用了莎拉·德拉斯纳的[夜猫子](https://github.com/sdras/night-owl-vscode-theme/)的调色板。

[![preview rubber-owl](img/df91332b2ecc74b87b09084f72707e1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yKVTV8nY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/VonHeikemen/rubber-themes.vim/master/assets/screenshot-rubber-owl.png)

## 立即获取

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[橡胶主题. vim](https://github.com/VonHeikemen/rubber-themes.vim)

### Vim 中最少量突出显示的配色方案集合。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 橡胶主题

Vim 中最少突出显示的配色方案集合。基于 VSCode 的[橡胶主题](https://github.com/apust/vscode-rubber-theme)。

受崇高文本[雪花石膏配色方案](https://github.com/tonsky/sublime-scheme-alabaster)相同前提的启发，它仅定义了四个类别:

*   用线串
*   已知常数(数字、符号、布尔值)
*   评论
*   功能

## 入门指南

### 可用主题

*   橡胶
*   橡胶猫头鹰

### 装置

**使用** [vim-plug](https://github.com/junegunn/vim-plug)

```
Plug 'VonHeikemen/rubber-themes.vim'
```

Enter fullscreen mode Exit fullscreen mode

### 激活

```
" For vim > 8
if (has("termguicolors"))
 set termguicolors
endif

" For Neovim 0.1.3 and 0.1.4
let $NVIM_TUI_ENABLE_TRUE_COLOR=1

syntax enable
colorscheme rubber
```

Enter fullscreen mode Exit fullscreen mode

## 截屏

*   橡胶

背景: #21252B

[端子的基色](https://github.com/VonHeikemen/rubber-themes.vim/blob/master/base-colors/rubber.yml)

[![preview rubber](img/f20a98ac1593931e8d55ab2be2bc18e4.png)](https://raw.githubusercontent.com/VonHeikemen/rubber-themes.vim/master/assets/screenshot-rubber.png)

*   橡胶猫头鹰

**背景:** #011627

[端子的基色](https://github.com/VonHeikemen/rubber-themes.vim/blob/master/base-colors/rubber-owl.yml)

[![preview rubber-owl](img/df91332b2ecc74b87b09084f72707e1c.png)](https://raw.githubusercontent.com/VonHeikemen/rubber-themes.vim/master/assets/screenshot-rubber-owl.png)

</article>

[View on GitHub](https://github.com/VonHeikemen/rubber-themes.vim)