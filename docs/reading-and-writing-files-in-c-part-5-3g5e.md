# 在 C 语言中读写文件:第 5 部分

> 原文：<https://dev.to/noah11012/reading-and-writing-files-in-c-part-5-3g5e>

正如我所承诺的，我们将编写并随后读回一个 C 结构！不过有一点需要注意:你需要了解二进制文件。可以在上一篇文章中了解更多。

为了测试，将使用以下结构:

```
struct TestStruct
{
    int    a;
    double b;
}; 
```

首先，需要连接到一个文件，特别是一个二进制文件。接下来，我们创建一个`TestStruct`的实例，并设置我们想要的值。最后，使用`fwrite()`将该结构写入文件。

```
#include <stdio.h> 
typedef struct
{
    int    a;
    double b;
} TestStruct;

int main(void)
{
    FILE *file = fopen("test", "wb");

    TestStruct ts;
    ts.a = 105;
    ts.b = 194.543;

    fwrite(&ts, sizeof(TestStruct), 1, file);
    fclose(file);
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
i!VL7�A`Qh@%                                                                    
~/Desktop 
➜ 
```

试图输出用 C 结构保存的文件会导致垃圾。

现在让我们读回数据，看看是否一切都是正确的。

```
#include <stdio.h> 
typedef struct
{
    int    a;
    double b;
} TestStruct;

int main(void)
{
    FILE *file = fopen("test", "rb");

    TestStruct ts;

    fread(&ts, sizeof(TestStruct), 1, file);
    fclose(file);

    printf("ts.a = %d\nts.b = %f\n", ts.a, ts.b);
} 
```

输出:

```
~/Desktop 
➜ clang main.c 

~/Desktop 
➜ ./a.out     
ts.a = 105
ts.b = 194.543000

~/Desktop 
➜ 
```

一切都写得很好，读得也很好。

## 接下来

这是一篇短文，还有更多的内容需要探索，但我觉得本系列中教授的所有内容将为许多人提供足够的知识来完成与文件相关的基本和更困难的问题。