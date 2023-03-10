# C/C++中的双指针

> 原文：<https://dev.to/noah11012/double-pointers-in-cc-2n96>

新 C 程序员感到困惑的一个原因是指针。乍一看，使用它们似乎没有多大用处。但是，理解指针是至关重要的，因为它是一个有用的工具，任何比“Hello，World”程序大的项目都会有指针。一旦初学者开始掌握指针的概念和适用性，另一波恐怖就袭向他们的内心深处:双指针。

在继续之前，让我们回顾一下什么是指针。

在 C 和其他语言如 C++中，指针是保存对象内存地址的东西。它们是一个数值，当输出到控制台时，通常以十六进制表示。这意味着，从本质上来说，指针只是花哨的整数。

现在回到双指针。

一看到双指针，初学者就开始不舒服地移动，汗水顺着额头一直流到下巴。就像生活中所有新鲜或陌生的事物一样，人们在面对从未遇到过的事物时会感到尴尬和不安，哪怕只是轻微的不安。但是随着你学得越来越多，你开始对你渴望征服的任何事物的新理解感到自信。双指针也是如此。

那么，什么是双指针呢？如果一个常规指针指向内存中的一个对象，那么双指针就是一个指向另一个指针的变量，而另一个指针又指向内存中的一个对象。

例子:

```
#include <stdio.h> 
int main(void)
{
    int value = 100;
    int *value_ptr = &value;
    int **value_double_ptr = &value_ptr;

    printf("Value: %d\n", value);
    printf("Pointer to value: %d\n", *value_ptr);
    printf("Double pointer to value: %d\n", **value_double_ptr);
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
~/Desktop 
➜ clang main.c

~/Desktop 
➜ ./a.out     
Value: 100
Pointer to value: 100
Doublue pointer to value: 100

~/Desktop 
➜ 
```

Enter fullscreen mode Exit fullscreen mode

当解引用一个双指针时，我们并没有得到最终的对象，而是得到了我们所期望的:一个必须再解引用一次才能得到最终值的指针。它类似于下面的代码。

```
int *ptr = *value_double_ptr;
int final_value = *ptr; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我确信大多数不熟悉双指针的人一定会问自己这样一个问题，“我会在哪里使用它呢？”。展示双指针有用性的最好方式可能是记住正则指针的一个实际用途。也就是说，正则指针可以在函数中作为输出参数使用。如果您碰巧不知道，输出参数是一个用结果填充的参数。为什么要使用输出参数取决于几个因素，这超出了本文的范围，但是我觉得这是可以使用双指针的地方。

我们要看的一个函数是 POSIX `getline()`函数。它的目的是从文件中读取一行。当行被读取时，人们可能会怀疑它被返回，但事实并非如此，因为返回值被用于不同的用途。相反，第一个参数采用双指针，用包含该行的缓冲区填充。

为什么要用双指针？因此，它可以分配足够的内存来保存整行加上空终止符。如果内存分配和读取一行成功，那么提供的指针被修改为指向新的和填充的缓冲区。只有使用双指针，他们才能做到这一点。

当向函数传递任何东西时，传递的是一个副本，而不是实际的对象。指针也是如此。如果我们传递一个常规指针，我们只能修改指针指向的内容。如果我们改变了指针本身，这种改变不会反映到函数之外，因为它是一个副本。

如果我们想改变指针指向的地方，我们必须添加另一个间接指针并使用一个双指针。

下面是一个使用`getline()`的例子。

```
#include <stdio.h> 
int main(void)
{   
    FILE *file = fopen("test", "r");

    char *line = 0;
    int   line_length = 0;
    getline(&line, &line_length, file);

    printf("%s\n", line);
    printf("Line length: %d\n", line_length);
} 
```

Enter fullscreen mode Exit fullscreen mode

`getline()`有两个输出参数:一个用于缓冲区，另一个用于读入的行的长度。

## 总结

当第一次学习时，双指针对许多初学者来说是一个挑战。随着您对双指针的概念越来越熟悉，并在必要时在您自己的代码中使用它们，您开始思考曾经害怕双指针是多么的愚蠢。

现在我带着你的新知识送你走！