# 具有 oh-my-zsh 的 git 的灵活性和效率

> 原文：<https://dev.to/alexmoreno/agilidade-e-eficiencia-no-git-com-oh-my-zsh-39pj>

FBI 已经说过，*胜利者不要使用 GUI* 。如果你喜欢 git kraken 的那些东西，崇高的合并等等，所有的颜色和可爱，这篇文章不是给你的。这里只剩下一个被黑得很厉害的人，终端上的黑鬼，当他还是个孩子的时候，他打开 windows cmd，一直在黑屏幕上打字，直到他不得不格式化 windows，因为他不小心删除了 system32。

你是不是厌倦了输入 git 的指令，伙计？你的工作流是面向 git 的，但你再也不能打开终端来创建分支，给它带来方便和便利了吗？这是一个袋子，不是吗，键入一堆垃圾，然后按 tab 键好几次，直到找到你想要的分支。我明白你的意思，我也深受其害。我很痛苦，因为现在我的问题已经解决了！你的还没有，但一旦你读了这篇文章就会结束。

首先，在你的机器上安装‘t0’【oh-my-zsh】。他很帅，你再也不想换了。接下来，仔细阅读它的外挂程式 [git](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh) 和 [git-flow](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/git-flow) 所拥有的权力:

### `gcd && ggpull`

`git checkout develop && git checkout origin git_current_branch`的捷径，也就是说，一个功能结束了吗？你出版了，去了那里做了公关？你被录取了吗？太好了。发送此命令，您的开发将立即反映远程“`develop`”。视您的流程而定，这可能是最有效的方法(如果您出于任何原因可以去那里接受您自己的 PR)，也可能不是最有效的方法(您需要代码审查，需要有人接受 pr 等)。)，然后再开始处理其他特征。

### `gapa [&& gc]`

`-p`的捷径(或干脆`-p`。听着，如果你觉得用`git commit -a`舒服的话，那就好。但我建议你看看`--patch`或`-p`的工作原理，因为它给你一个难以置信的程度的控制什么去实习或不去实习。我习惯了使用指挥棒后，现在才使用。或者我给`gapa`或者我给`gc -p` ( `git commit -p`，不幸的是欧姆士的 git 插件没有用于`git commit -p`的原生插件，`gcp`已经指`git cherry-pick`

### `gcb nome-da-branch`

`git checkout -b nome-da-branch`的捷径。这个命令给你的灵活性是很棒的。不要再键入“`git checkout branch`”并注意到忘记了标记，然后重写命令或向上按 Seth，将光标放在正确的位置，键入-b 并按 enter 键。使用此命令，您不会出错。简短、实用和有效。

### `gcam "mensagem do commit"`

`git commit -am ""mensagem do commit"`的捷径。好吧，我不建议你使用这个命令，但如果你过洛卡生活，全 XGH，无刹车的玻利维亚，去快破东西，发子弹。我承认我已经用过很多次了但多亏了你的出现我才摆脱了困扰我的邪恶。

### `/ggpu(sh|ll)/`

分别为`git push origin git_current_branch`和`git pull origin git_current_branch`的捷径。就像`gcb`一样，这是我最高兴学习的命令之一。不再需要键入分支名称的前两个字母，然后按 tab 键直到找到它们！永远不要迷路！胡雷，快点！

### `gcm`

有一天，我是一个在硕士学位上做事情的年轻人。所以我开始从大师那里创造树枝。今天冷运会给我介绍了许多困扰我的问题的解决方案和光明之路。但如果还需要经常去‘t0’，则`gcm`是实现这一点最快捷的方式。

### `ggu`

`git pull --rebase origin git_current_branch`的捷径如果你来自# team excel，这个命令就是你的新宠。`ggu`就这样。就在你手上，小子。

### [T3`gl`e`gp`](#-raw-gl-endraw-e-raw-gp-endraw-)

`git push`和`git push`的捷径。快速的通用性。`gl`和`gp`之后，你可以随心所欲地用你的指挥论证，在指尖拥有两个世界中最好的。

### `gdba`

*男孩，将此命令与*一起使用*小心*。或者不，就像我们一样，你知道，你搞砸了 git？cd(光盘)...我...这个命令把水槽里所有的垃圾都扔了你这个懒家伙！也不能丢垃圾，tsc-我...。已合并的所有分支(master、develop 和 dev 除外)都将被删除，但仅限于本地分支。也就是说，就地告别堆积如山的老枝！

### `gc!`

`git commit -v --amend`的捷径。经典之作。我输入了错误的信息，直到我离开后才意识到？`gc!`改正、保存、退出。越是阅读障碍，就越有用。

### `glo`

`git log --oneline --decorate`的捷径。在 oneline 上查看所有承诺，散列、合并和*cor*。包括，我建议你测试一下，仔细看一下`git log`所有可能的捷径。花费的时间肯定值得。

### [T3`gsta`e`gstaa`](#-raw-gsta-endraw-e-raw-gstaa-endraw-)

`git stash apply`和`git stash apply`的捷径。分别将所有内容放在最新的 stash 中，然后将最新的 stash 应用到项目中。当您在切换分支*时非常有用，因为您留下了一些不稳定的变化*

### `gwch`

`git whatchanged -p --abbrev-commit --pretty=medium`的捷径。`git whatchanged`与□很相似，但会显示每次提交时更改的文件。`-p`旗标也会显示程式码中的变更。`--abbrev-commit`显示哈希；`--pretty=medium`是提交格式。我很少使用它，但我很高兴知道它在我需要的时候存在。

### 自己的别名

我绝对鼓励(我承认我也应该做更多)自己使用 zsh 命令的捷径。您是否有经常输入的任何脚本？去那里，一起为他们做一个‘t0’。你知道那些你喜欢使用的个性化的`git log`或`git status`？`alias`在他们身上。你可以在你的外壳文件(`.zshrc`或你的 git 中做，使用 [`git config`](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases) 。我喜欢留在我的嫁妆箱里，因为它更容易随身携带。例如，我喜欢`git log --oneline --decorate --no-merges`，所以在我
的末尾留一个`alias gl=comando`也做你自己的，每一个击键除非你给，少了一个导致你成为合法黑客 3rboy 的击键。

# 啊，群众的那股去流？

很高兴你这么问。我们也有敏捷的 git flow，这也是敏捷的。递归灵活性。天啊！

您需要在 zsh 中安装 git flow 插件。

你看，这个可以把所有的事情都记录在你的脑海里只要你理解他们的缩写是怎么运作的。
基本命令:

`g`=>= T1】

`fl`=>= T1】

分支类型:

`f`=>= T1】

`h`=>= T1】

`r`=>= T1】

gitflow 特定命令:

`s`=>= T1】

`f`=>= T1】

`p`=>= T1】

不久，如果我想给一个`g` + `fl` + `f` + `s`，即`gflfs`。我的朋友们。

### 实践中的敏捷性

`git flow`已经让东西超简单地在你的代码库里产生东西了。你看，当你的舌尖有了捷径，事情变得很容易爬上一个特征。

*   一、`gflfs nome-da-feature`启动功能。
*   然后编码。
*   Terminou？`gapa && gc`(欧`gc -p`)。
*   你提交了吗？你没事吧？`gflfp`自动远程发布，一切正常。
*   您的远程分支机构是否已获得批准并合并到远程开发中？`gcd && ggpull`及其位置已经更新。

皮巴。比这更简单，只有一个克隆你为你这样做。

你还有什么要补充的吗？你把 git.plugin.zsh 搞砸了发现了一些有用的东西？在评论中说，以后找到这篇文章的人会用‘t0’的知识阅读它。