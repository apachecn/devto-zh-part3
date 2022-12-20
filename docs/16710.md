# 在 C 语言中读写文件:第 2 部分

> 原文：<https://dev.to/noah11012/reading-and-writing-files-in-c-part-2-6k1>

本系列的上一篇文章讨论了字符以及使用带有`fgetc()`和`fputc()`的循环来读写一系列字符或字符串。读取或写入一系列字符是如此常见的操作，以至于 C 标准文件 I/O 库为我们提供了这种功能是有意义的。

我们的演示程序将使用`fputs()`编写一行文本。

`fputs()`和`fputc()`几乎完全一样，除了，正如你可能已经猜到的，它写的是一个字符串而不是一个字符。对于成功的写操作，`fgets()`的返回值是一个非负数，而对于错误操作，`EOF`的返回值是一个非负数。

示例:

```
#include <stdio.h> 
int main(void)
{
    FILE *f = fopen("test", "w");
    char *message = "I am a line with in a file\n";
    fputs(message, f);
} 
```

输出:

```
~/Desktop 
➜ clang main.c 

~/Desktop 
➜ ./a.out 

~/Desktop 
➜ cat test 
I am a line with in a file

~/Desktop 
➜ 
```

我们在文件中写了一行文本，把它读回到程序中是有意义的。在文件 I/O 的读取端，我们有一个字符串版本的`fgetc()` : `fgets()`。

`fgets()`的第一个参数接受一个指向 char 数组的指针，该数组可以保存您要从文件中读取的字符串。第二个参数告诉我们要读入多少个字符，指定的字符将减 1。例如，如果要读入 100 个字符，`fgets()`最多会读入 99 个字符。第三个也是最后一个参数是一个指向`FILE`对象的指针。

成功时`fgets()`的返回值是指向作为第一个参数提供的 char 数组的指针。出错时，返回一个空指针。

示例:

```
#include <stdio.h> 
int main(void)
{
    FILE *f = fopen("test", "r");
    char message[100];
    if(fgets(message, 27, f) == NULL)
    {
        printf("Error when reading string!\n");
        return 1;
    }
    printf("%s\n", message);
} 
```

输出:

```
~/Desktop 
➜ clang main.c 

~/Desktop 
➜ ./a.out 
I am a line with in a file

~/Desktop 
➜ 
```

## 接下来

在本系列的下一篇文章中，我们将更多地讨论 EOF 和可能发生的错误，以及如何区分这两者。