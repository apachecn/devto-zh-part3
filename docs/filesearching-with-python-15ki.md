# 使用 Python 进行文件搜索

> 原文：<https://dev.to/himan10/filesearching-with-python-15ki>

嗨伙计们...我设计了一个用于搜索的 python 程序，将文件从搜索到的位置发送到新创建的目录。

它可以从任何位置搜索任何文件，也可以发送权限受限的文件(我不从 os.walk()函数中排除任何点文件，但不包含任何内容的文件夹或没有任何文件的文件夹除外)。

文件搜索是如何工作的？？
- >因此，文件搜索的主要工作依赖于一些函数和模块，

1.  OS -这个模块帮助我实现了它的几个功能，这样我就可以和我的操作系统进行通信了。

2.  Shutil -帮助将文件从源位置发送到目标位置。

3.  os.walk() -帮助遍历文件夹、子目录、文件的列表。

4.  Regex -帮助创建一个搜索特定文件的模式(基于一些扩展名。mp3、pdf、7z、bz、mp4 等等)。

这四个杰作帮助我完美地完成了我的工作，还有其他功能，但这四个就像一个蓝图，以便最终用户可以了解文件搜索是如何工作的，甚至不用检查每一行代码。

项目仍在开发中，如果任何人需要查看源代码或测试项目，那么他会检查我的 GitHub 回购。文件搜索->

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ Himan10 ](https://github.com/Himan10) / [文件搜索](https://github.com/Himan10/FileSearching)

### 使您的文件搜索(基于扩展名)更加准确和有效

<article class="markdown-body entry-content container-lg" itemprop="text">

# 文件搜索是关于-

Well 文件搜索程序为您提供了从系统的任何地方搜索文件(基于扩展名)的更好方法。

它还在新创建目录中提供搜索项的副本，以及由路径组成的文件。这里路径指的是这些项目所在的位置。

用户需要提供文件扩展名和位置，以便搜索项目，作为回报，用户有搜索项目的名称/路径，并能够复制搜索的项目。

例如:

```
input -> mp3
         location (/home/Downloads , /usr/bin)
output -> Names/Paths of mp3 files
                or
          Copy files to a newly created directory 
```

# 这是如何运作的-

-> OS-[https://docs.python.org/3/library/os.html](https://docs.python.org/3/library/os.html)

-> Shutil - [https://docs.python.org/3/library/shutil.html](https://docs.python.org/3/library/shutil.html)

-> Regex-[https://docs.python.org/3/library/re.html](https://docs.python.org/3/library/re.html)

-> Sys-[https://docs.python.org/3/library/sys.html](https://docs.python.org/3/library/sys.html)

```
# -> os.walk()

os.walk() helps me to iterate over a list of folders, subfolders, files

Using os.walk(path) we
```

…</article>

[View on GitHub](https://github.com/Himan10/FileSearching)

这是我在 dev.to 社区的第一篇文章...所以，伙计们请看回购。并提出改进建议。

谢谢你