# 在 C 语言中读写文件

> 原文：<https://dev.to/noah11012/reading-and-writing-files-in-c-4881>

如果您曾经使用过更高级语言的文件 I/O 功能，如 Python 或 Rust，您可能会想，操纵文件来解决您的问题是多么容易，即使是读入配置文件、将文件解析为标记，或者只是将信息写入磁盘。也许你想知道 C 中的文件 I/O 是如何工作的？*

*当然，没有人会读心术，包括我。

## 阅读一个字符

最基本的文件 I/O 从读/写字符开始。C 标准 I/O 库分别提供了读写字符的函数`fgetc()`和`fputc()`。

它们的工作原理非常简单。两者都操作文件流，或者在 C 语言中更常见的,`FILE`类型。这种类型是一种跟踪许多不同状态的结构，比如读/写了多少个字符、错误状态等等。我们从不直接查看或更改保存在`FILE`中的信息，相反，我们使用提供给我们的函数来检索这些信息。所有处理文件的 C 标准 I/O 函数都反映了这一点。他们都把指针指向一个`FILE`。

有了介绍性的文字，我们可以开始使用 c 语言中的文件。

首先，我们需要获得一个`FILE`对象用于我们的文件冒险。我们如何得到一个？具有`fopen()`功能。

很简单:第一个参数是文件的路径，第二个是模式。

**模式？那是什么？**

文件 I/O 上下文中的模式指的是我们对文件拥有什么权限以及如何打开文件。权限告诉我们是否可以读、写，或者两者都可以。根据权限的不同，文件的打开方式可能有不同的含义。

在`fopen()`中，模式是一个字符串。

1.  r
    阅读

2.  w
    写

3.  阅读和写作

4.  阅读和写作。如果找不到，则创建该文件

5.  一个
    从文件的末尾开始写。如果找不到，则创建该文件

6.  阅读和写作。如果找不到，则创建该文件。读取的初始位置从开始处开始，但写入时会附加到文件中

出错时，`fopen()`返回空指针，成功时返回`FILE *`。

示例:

```
#include <stdio.h> 
int main(void)
{
    FILE *file = fopen("test.txt", "w");
    if(file == NULL)
    {
        // handle error
    }

    // use file
} 
```

首先，假设我们想只使用`fputc()`将消息写入文件。显然，我们首先需要的是字符串形式的消息。就我们的目的而言，这可能只是一个字符串。

```
#include <stdio.h>
#include <string.h> 
int main(void)
{
    FILE *file = fopen("message.txt", "w");
    if(file == NULL)
    {
        printf("Failed to open file!\n");  
        return 1;
    }

    char *message = "I am a file\n";
    int i = 0;
    while(i < strlen(message))
    {
        fputc(message[i], file);
        i++;
    }
} 
```

输出:

```
~/Desktop 
➜ clang main.c

~/Desktop 
➜ ./a.out

~/Desktop 
➜ cat message.txt 
I am a file

~/Desktop 
➜ 
```

这对于演示目的来说是完美的，但是在现实世界中，一些事情可能会在内部失败，你的角色可能会被写出来。`fputc()`如果成功，朋友返回写的字符，但`EOF`出错。`EOF`是一个宏，代表 **E** nd **O** f **F** 文件，扩展为一个常量整数表达式。通常，它由读入字符的函数返回，让调用代码知道最后一个字符已被读取。在其他类似`fputc()`的情况下，这意味着发生了错误。

带有错误检查的更新版本:

```
#include <stdio.h>
#include <string.h> 
int main(void)
{
    FILE *file = fopen("message.txt", "w");
    if(file == NULL)
    {
        printf("Failed to open file!\n");
        return 1;
    }

    char *message = "I am a file\n";
    int i = 0;
    while(i < strlen(message))
    {
        int result = fputc(message[i], file);
        if(result == EOF)
        {
            printf("Failed to write a character!\n");
            return 1;
        }
        i++;
    }
} 
```

好多了。如果出了问题，一旦发生，我们就会知道。

接下来，如果我们试图从同一个文件中读取数据呢？除了模式被设置为 reading 并且`while`循环现在从文件中一个字符一个字符地读取之外，代码基本上是相同的。

示例:

```
#include <stdio.h>
#include <string.h> 
int main(void)
{
    FILE *file = fopen("message.txt", "r");
    if(file == NULL)
    {
        printf("Failed to open file!\n");
        return 1;
    }

    char message[100];
    int c = 0;
    int i = 0;
    while((c = fgetc(file)) != EOF)
    {
        message[i] = c;
        i++;
    }

    printf("%s", message);
} 
```

输出:

```
~/Desktop 
➜ clang main.c 

~/Desktop 
➜ ./a.out 
I am a file

~/Desktop 
➜ 
```

## 接下来

在下一篇文章中，我们将讨论文件 I/O 的其他可用功能。