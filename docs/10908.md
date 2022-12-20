# 使用 Python 3 的 pathlib 模块自动完成枯燥的工作。

> 原文：<https://dev.to/mekicha/automate-the-boring-stuff-with-python-3s-pathlib-module-i7b>

在 Python 3.4 中引入了`Pathlib`模块，目的是:

> 提供一个简单的类层次结构来处理文件系统路径和用户对它们进行的常见操作。

在本文中，我打算介绍一些常见的操作。最后，我展示了如何使用`Pathlib`模块和一些 cron 作业来保持计算机上的下载文件夹整洁有序。让我们开始吧。

根据[文件](https://docs.python.org/3.5/library/pathlib.html):

> 如果您以前从未使用过这个模块，或者不确定哪个类适合您的任务，Path 很可能是您需要的。它为运行代码的平台实例化了一个具体的路径。

这是可以理解的。那么让我们导入`Path`类。

```
In [9]: from pathlib import Path

```

如果你正在使用类似于 [Ipython](https://ipython.org/) 或任何其他的交互式 shell，那么`dir`导入的类或对象来获得关于类提供的属性和方法的线索通常是一个好主意。

### 获取用户主目录

```
# returns PosixPath('/home/mekicha')
# This is equivalent to os.path.expanduser('~') home_dir = Path.home() 
```

### 获取主目录中的一个目录

```
# returns PosixPath('/home/mekicha/Downloads')
# Equivalent to: home_dir.joinpath('Downloads') downloads_dir = home_dir / 'Downloads'

downloads_dir.exists() # True 
```

现在，在这一点上，您可能会想:“这就是这篇许诺向我展示使用 Pathlib 模块的实际例子的点击诱饵文章的全部内容吗？”。在你因浪费时间和浪费时间而愤怒地结账之前，我给你这个紧迫的问题一个冷静的回答:“不，朋友，事情还没完”。

事实上，如果这就是全部的话，我会把你带到[文档](https://docs.python.org/3.5/library/pathlib.html)中，那里有很多这样的例子。
我只是提供了上面的例子，作为未来更好事情的某种形式的基础工作(希望如此)。
现在，你对以下例子有什么看法。

### 给定一个目录，计算所有文件的总大小

```
from pathlib import Path

def get_size(directory: Path):
  """ Given a directory, return total size of files in bytes """
  total_size = 0
  for file in directory.iterdir():
     total_size += file.stat().st_size  # in bytes
  return total_size

if __name__ == '__main__':
  # Let's get the size of my home directory
  directory = Path.home()
  size_in_byte = get_size(directory)

  # Nice to have: convert the size to megabyte or gigabyte 
  mega = 1 << 20
  giga = 1 << 30 
  if size_in_byte / giga < 1:
    if size_in_byte / mega < 1:
      print(f'file size = {size_in_byte} bytes')
    print(f'file size = {size_in_byte / mega} megabytes')
  print(f'file size = {fize_in_byte / giga} gigabytes' 
```

### 给定一个目录，返回最大尺寸的文件

```
def get_max_file(directory: Path):
  return max((f.stat().st_size, f) for f in directory.iterdir())

# In the same vein, let's get last modified file in a directory 
def get_last_modified(directory: Path):
  return max((f.stat().st_mtime, f) for f in directory.iterdir()) 
```

### 给定一个目录，返回该目录下的子目录列表

```
def get_dir_list(directory: Path):
  return [child for child in directory.iterdir() if child.is_dir()] 
```

### 整理我电脑的下载文件夹

如果你已经读到这里，希望这是你要找的东西的开始。

我的下载文件夹以前很乱。不同格式的文件以一种无助无序的方式并排躺在一起，每一个都在呼唤我的注意，也许当我不点击它们的时候会感觉被忽视了。不再是了。今天，他们得到了帮助。我要把文件放在它们应该在的地方。图像应该放在图像子文件夹中，文档(比如 pdf、doc、xls)应该放在文档文件夹中，压缩文件放在压缩文件夹中是正确的。我将指派一名克隆代理来完成这项工作。

cron 代理将被设置为每天运行一次，在我应该睡觉的时候运行。

下面是代码:

```
#!usr/bin/env python3 
import time
import logging
from pathlib import Path

logging.basicConfig(level=logging.DEBUG)

logger = logging.getLogger(__file__)

def organize_directory(directory: Path):
    """ Take a path to a directory and organizes the files there
        into subdirs.
     """

    sub_dirs = ['images', 'docs', 'zipped','media', 'others']
    images = ['.png', '.jpeg', '.jpg', '.gif']
    zipped = ['.zip', '.gz', 'tgz']
    docs = ['.doc', '.docx', '.pdf', '.xls', '.xlxs', '.PPT', '.txt']
    media = ['.mp4', '.mkv', '.avi', '.mp3']

    # create base subdirs.
    # Map each subdir to its subdir path
    dir_map = {}
    for subdir in sub_dirs:
        subdir_path = directory.joinpath(subdir)
        subdir_path.mkdir(parents=True, exist_ok=True)
        dir_map[subdir] = subdir_path

    start_time = time.monotonic()
    for child in directory.iterdir():
        child_name = child.name
        if child.is_file():
            if child.suffix in images:
                target = dir_map['images'].joinpath(child_name)
                child.replace(target)
                logger.info(f'moved {child} to images folder')
            elif child.suffix in docs:
                target = dir_map['docs'].joinpath(child_name)
                child.replace(target)
                logger.info(f'moved {child} to docs folder')
            elif child.suffix in zipped:
                target = dir_map['zipped'].joinpath(child_name)
                child.replace(target)
                logger.info(f'moved {child} to zipped folder')
            elif child.suffix in media:
                target = dir_map['media'].joinpath(child_name)
                child.replace(target)
                logger.info(f'moved {child} to media folder')
            else:
                target = dir_map['others'].joinpath(child_name)
                child.replace(target)
                logger.info(f'moved {child} to others folder')

    end_time = time.monotonic() - start_time
    logger.info(f'Organizing directory took {end_time} seconds')

if __name__ == '__main__':
    download_dir = Path.home().joinpath('Downloads')

    organize_directory(download_dir) 
```

再一次，文档中关于 [iterdir](https://docs.python.org/3.5/library/pathlib.html#pathlib.Path.iterdir) 、 [mkdir](https://docs.python.org/3.5/library/pathlib.html#pathlib.Path.mkdir) 和 [replace](https://docs.python.org/3.5/library/pathlib.html#pathlib.Path.replace) 的条目对他们做了什么非常清楚。

运行上面的脚本给了我这个结果:

`INFO:download_folder_agent.py:Organizing directory took 0.01179114996921271 seconds`

这还不是全部。它也给了我一个组织得更好的下载文件夹。

### 设置 cron 作业

设置 cron 作业相当容易。

以下是我采取的步骤，它们给了我想要的东西:

1.  通过键入:`crontab -e`打开 crontab 编辑器
2.  在[crontab-guru][crontab guru]上查找 crontab 计划表达式。我选择每天早上 5 点。0 5 * * *
3.  在文件中添加以下作业条目:` 0 5 * * * python 3/home/meki cha/agent/download _ folder _ agent . py > >/home/meki cha/agent/log 2 > & 1
4.  我保存了更改并退出了文件。5.瞧啊。一切正常。

实际上，我首先将时间表设置为每分钟运行* * * * *，在提交上面的时间表之前检查它是否工作。

我将时间表设置为每天早上 5 点运行，这并不理想。我又不是时不时就下载东西。但是我现在就说到这里。请随意选择你认为最好的。

如果你想知道这个 2>&1 的东西是什么意思，这个 [stackoverflow](https://stackoverflow.com/questions/818255/in-the-shell-what-does-21-mean) 的答案是我在想这个问题时读到的。

好吧，我想这是结束整件事的好地方。在自动化枯燥内容的下一集里，我将展示如何通过删除已经过期的文件来保持下载文件夹的整洁。

谢谢大家！

附注:这个自动化无聊的东西系列文章是对同名书的模仿。