# 自动化 conda 卸载的快速方法

> 原文：<https://dev.to/perigk/a-quick-way-to-automate-your-conda-uninstalls-jmk>

距离我的上一篇文章已经有一段时间了，说实话，这几个月对我来说要求太高了，不仅是在我的职业生涯中。不过，在过去的这段时间里，我做的最好的事情之一是在代码的第一年向[提交了一个章节(感谢@isaacdlyman 给我这个机会:)](https://leanpub.com/firstyearincode)

今天，我将展示一个我制作的 python 脚本，来帮助你卸载所有匹配特定过滤器的 anaconda 包。

这是整个剧本。没什么，没什么值得一个图灵奖的，只是觉得会对某个人有用。

```
import os

# Example function, not uninstall packages with the 
# digram py in their name. Please update accordingly
def filter_function(name):
    return 'py' not in name

# Get the installed conda packages
conda_list_output = os.popen('conda list').read()

# Put them in a list
output_in_lines = conda_list_output.split('\n')

# Line 4 and onwards is what we want (feel free to examine it)
output_in_lines = output_in_lines[3:]

# Name appears in the beginning of the line
only_names = [line.strip().split(' ')[0] for line in output_in_lines]

# Apply the filter defined above
filtered_list = list(filter(filter_function, only_names))

# Convert the list of uninstall candidates to a string
# and run the unins
cmd = f'conda uninstall {" ".join(filtered_list)}'

os.system(cmd) 
```

文件中有大量的日志记录，但是如果你是一个 TL；盖伊博士

*   我们得到了包的列表
*   我们清理它，并将其转换为友好的格式
*   我们过滤掉不需要的
*   我们构建并运行卸载命令

你唯一需要改变的是过滤函数，你可以让过滤器变得越复杂越好

感谢您阅读这篇非常短的文章。让我知道这是否有帮助。:)