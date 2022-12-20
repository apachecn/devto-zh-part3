# 使用不匹配的 python 版本解决 virtualenvwrapper 的问题

> 原文：<https://dev.to/2upmedia/solving-problems-with-virtualenvwrapper-mismatched-python-versions-57el>

**TLDR**

*   理解路径的优先级
*   了解路径通常设置在哪里
*   确保您的 python 版本正在使用相应的 pip shell 命令

虽然这是专门针对 virtualenvwrapper 的，但它可以帮助您找出您安装的 shell 命令由于某种原因无法工作的原因。

如果您曾经使用过 virtualenvwrapper，您就会知道混合搭配 python 版本并为每个项目建立独立的依赖库是多么有用，这样您就不会为每个项目使用相同的 pip。在极少数情况下，如果您安装了多个版本的 python，virtualenvwrapper 可能会给您带来问题。

在我的例子中，当指定不同于默认的 python 解释器时，mkvirtualenv 会失败。我想使用 python 3.7，它从 python 2.7 中挑选 pip 包。我使用的命令类似于`mkvirtualenv -p python3 my-env`。

在分析我的设置后，有几个问题。我有几个不同来源的 python 版本，有些来自 macports，有些来自 homebrew，还有预装在 Mac OS 中的内置 python。

为了解决正在发生的事情，我必须理解这个问题。最终的问题是 python 3.7 试图为 python 2.7 使用 pip。从 python 异常中可以明显看出这一点，很明显，所使用的解释器是 python 3.7，但是堆栈跟踪显示的是引用 python 2.7 的路径。

我必须确保为 python 3.7 安装了正确的 pip。家酿，至少在我的机器上，采用了最后一个条目 macports bin 路径优先于(首先出现在路径中)家酿 bin 路径。理解这一点很重要，因为 PATH 的工作方式非常简单。首先定义的路径将首先用于查找您想要运行的 shell 命令。所以如果你运行`$ python`并且在路径中首先定义了`/opt/local/bin`，那么它将解析为`/opt/local/bin/python`。

我想我可以把 macports python 从图片中去掉，看看它是不是导致问题的那个。为此，我使用了`port select python none`。据我所知，这样做是从 macports bin 路径中删除了符号链接。出于某种原因，这没有做任何奇怪的事情。所以我需要做更多的调查。

我记得要留意 PATH 中的内容，因为它基本上控制着首先选择哪个 shell 命令。当我这么做的时候，我注意到仍然有对 macports python 的引用。那绝对不应该发生。我已经跑完了`port select python none`。$PATH 环境在多个地方设置。总的来说，这里有几个优先级别最高的地方:

*   /etc/profile
*   /etc/bashrc
*   ~/.bash_profile
*   ~/.轮廓
*   ~/.bashrc

罪魁祸首在~/.profile 中，在 macports python 版本`export PATH=/opt/local/Library/Frameworks/Python.framework/Versions/2.7/bin:$PATH`的$PATH 前有一个条目。在我注释完这个之后，我的 shell 按照我的意愿选择了自制版本。我通过运行`which python`验证了这一点，并看到它是从家酿箱路径`/usr/local/bin`而不是从 macports 箱路径`/opt/local/bin`拾取的。但是有一个问题，自制 python 命令实际上是 python 2.7，而不是我想用的 3.7。

我发现家酿安装 python 3.7 是 python 3。我本可以从 python3 python 中创建一个符号链接，但之后我需要记录我实际上是这样做的。Macports 有一个`port select`命令，我可以在那里更换同一程序的不同版本，但 homebrew 没有这个功能。最接近的是 brew link 和 unlink，但是和 macports 的工作方式不一样。它所做的只是将程序从自制程序的 bin 路径中移除。所以这对我没什么帮助。

当我回溯时，我想起了由于某种原因 python 3.7 试图使用为 python 2.7 安装的 pip。当我运行`brew link -vd`向我展示详细输出时，没有任何对 pip 的引用，我发现这非常可疑。经过一番谷歌搜索，发现有人推荐做 brew 重装 python。这最终成为为 python 3.7 安装 pip 的关键命令。它最终使用安装 pip，但 brew 将二进制文件命名为 pip3，而不是 pip。在家酿中，pip 是针对 python 2.7 的。

下一部分非常重要，因为它不仅仅是指定您想在`mkvirtualenv -p python3`中使用的 python 解释器版本那么简单。似乎 virtualenvwrapper 默认使用当前安装的 python 版本和 pip 版本。正如我之前提到的，安装的是来自 homebrew 的 python 2.7。因此，为了避免这个问题，virtualenvwrapper 有一个环境变量，您可以将它设置为 python 解释器，您希望将它用于 virtualenvwrapper 本身。叫做 VIRTUALENVWRAPPER_PYTHON。您将它设置为您想要使用的 python 解释器的绝对路径。我做了
`export VIRTUALENVWRAPPER_PYTHON=`which python3``。现在当我运行`mkvirtualenv -p python3`时，它没有抛出任何试图使用 python 2.7 的 pip 的 python 异常。