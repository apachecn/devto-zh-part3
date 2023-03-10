# 在 C 语言中读写文件:第 3 部分

> 原文：<https://dev.to/noah11012/reading-and-writing-files-in-c-part-3-i4l>

在前一篇文章的最后一节，我承诺我们将讨论一些可能发生的不同错误。虽然没有必要了解可能出现的各种错误，但是了解在处理文件时可能出现的各种错误是有教育意义和有益的。

### 移动/删除

对于某些程序，文件用于记录程序提交的某些操作和错误的日期和时间。如果用户或其他程序将文件移动到另一个位置或删除它，那么理论上写操作可能会失败，程序将(有希望)正常恢复。这通常包括重新创建文件，并写入程序选择记录的所有新条目。这种情况很可能不会发生在短期文件上，因为顾名思义，用户移动或删除这些文件的时间不会很长。在 Windows 中，如果文件是由进程打开的，则不允许移动或删除它。在基于 Unix 的操作系统上，移动或删除文件被认为是对文件所在的目录进行操作。如果打开并删除，它将保留在磁盘上，直到关闭。

### 关闭后的操作

每个操作系统都为我们公开了一个 API 来获取只有它才能提供的资源。这些资源之一是文件。在像 Linux 这样基于 Unix 的操作系统上，系统调用`open()`是存在的。在允许我们打开文件之前，操作系统必须确保我们有权限这样做，当然，在硬盘上存在。一旦操作系统断定我们没有违反任何权限，并且可以在磁盘上找到文件，它将返回一个 ID 供我们使用。这个 ID 是一个数值，在我们程序的上下文中，它被分配给一个打开的文件。当我们完成这个文件时，我们必须让操作系统知道关闭这个文件。对于像 Linux 这样的 Unix 基本操作系统，`close()`会这样做。在 C 标准库文件 I/O 库中，这是`fclose()`。对已关闭的文件进行任何操作都会导致错误。

示例:

```
#include <stdio.h> 
int main(void)
{
    FILE *file = fopen("test", "r");
    fclose(file);
    char buffer[100];
    if(fgets(buffer, 5, file) == 0)
    {
        printf("Error reading file!\n");
        return 1;
    }
    printf("%s\n", buffer);
} 
```

输出:

```
~/Desktop 
➜ clang main.c 

~/Desktop 
➜ ./a.out 
Error reading file!

~/Desktop 
➜ 
```

### 满储

如果当前正在使用的存储介质达到了最大存储容量，那么除了通过删除文件来释放空间之外，就没什么可做的了。

### 硬件故障

故障硬盘可能会导致文件 I/O 操作失败。由于不断恶化的存储介质的不稳定性，您甚至可能不会从操作系统得到错误，但仍然是一个错误，因为读/写的数据可能已损坏。

## 接下来

在下一部分，我们将讨论如何在文件中不仅仅保存文本。