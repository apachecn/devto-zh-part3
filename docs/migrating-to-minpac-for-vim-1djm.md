# 为 Vim 迁移到 minpac

> 原文：<https://dev.to/leesmith/migrating-to-minpac-for-vim-1djm>

这篇文章最初发表在我的博客上。

我刚刚对我的[点文件](https://github.com/leesmith/dotfiles)做了一次彻底检查，我想记录我是如何以及为什么从[病原体](https://github.com/tpope/vim-pathogen)迁移到 [minpac](https://github.com/k-takata/minpac) 的。

## 为什么选择 minpac

Minpac 被描述为“Vim 8 的最小软件包管理器”。好的，很好。minpac 与已经存在多年的包管理器(Vundle、vim-plug、pathogen 等)有什么不同或更好的地方？)?

现在 Vim 8 附带了原生包管理，minpac 建立在这个功能之上，允许一个更轻量级的插件。与其前任不同，minpac 没有发明自己的管理运行时路径的解决方案。minpac 利用的另一个 Vim 8 特性是作业控制特性。这允许 minpac 并行安装和更新插件。

## 配置 minpac

所以对我来说，来自病原体，我可以删除`~/.vim/bundle`目录，创建 minpac 将居住的目录。

```
rm -rf ~/.vim/bundle
mkdir -p ~/.vim/pack/minpac/opt
git clone https://github.com/k-takata/minpac.git ~/.vim/pack/minpac/opt/minpac 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，就是为 minpac 配置您的`.vimrc`的问题了。以下是你可能在`.vimrc`中包含的内容的基本示例。Minpac 的安装说明提供了比这更简单的东西，但我喜欢这个例子，因为它提供了更灵活的`.vimrc`的能力，以适应您想要在不同环境中使用相同`.vimrc`的情况(比如在没有安装您的插件的服务器上)。

```
" ~/.vimrc
" Try to load minpac.
packadd minpac

if !exists('g:loaded_minpac')
  " minpac is not available.

  " Settings for plugin-less environment.
  " ...
else
  " minpac is available.
  " init with verbosity 3 to see minpac work
  call minpac#init({'verbose': 3})
  call minpac#add('k-takata/minpac', {'type': 'opt'})

  " Additional plugins here.
  call minpac#add('airblade/vim-gitgutter')
  call minpac#add('altercation/vim-colors-solarized')
  call minpac#add('itchyny/lightline.vim')
  call minpac#add('prettier/vim-prettier')
  call minpac#add('tpope/vim-commentary')
  call minpac#add('tpope/vim-endwise')
  call minpac#add('tpope/vim-fugitive')
  call minpac#add('tpope/vim-repeat')
  call minpac#add('tpope/vim-surround')
  " ...

  " minpac utility commands
  command! PackUpdate call minpac#update()
  command! PackClean call minpac#clean()
  command! PackStatus call minpac#status()

  " Plugin settings here.
  " ...
endif

" Common settings here.
"... 
```

Enter fullscreen mode Exit fullscreen mode

这是 minpac 的实际应用。在我打开 vim 之后，我使用实用程序命令`:PackUpdate`更新我的`.vimrc`中指定的所有插件。更新完成后，`:PackStatus`显示了所做更改的摘要。

[![Minpac](img/0b8dbd4c752e28e21beccfbe1fb2406c.png "Minpac")](https://res.cloudinary.com/practicaldev/image/fetch/s--sma3jIEj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9vxctcz2blgzmdvax1w1.gif)

当然，如果你更喜欢 Neovim 8，minpac 也可以和 Neovim 一起工作。查看文档，了解稍微不同的安装过程。

祝你快乐！🍻