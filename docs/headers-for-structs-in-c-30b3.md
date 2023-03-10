# C #中结构的标头

> 原文：<https://dev.to/noah11012/headers-for-structs-in-c-30b3>

我说的结构中的“头”是什么意思？在其他编程环境中也是同样的符号。最常见的例子是文件格式的头或 HTTP 头。

例如，许多图像格式，如 JPEGs 和 TIFFs，都有详细描述图像宽度、高度、位深度等的标题。

我们当然可以用 c 语言的结构做类似的事情。

在本文的其余部分，我们将实现一个名为 CStr 的虚拟库。我们的目标是拥有一个非常容易使用的库，就像人们在语言中使用原始 C 字符串时所习惯的那样。

```
char *name = c_str_new("Noah");
printf("Your name is %s", name); 
```

需要一个结构来保存字符串的额外信息，比如它的长度和容量。

```
typedef struct
{
    int length;
    int capacity;
} CStrHeader; 
```

但是，当创造它的时候，它将走向何方呢？就像图像头一样，我们将它放在内存中的字符串之前。

因为头就在字符串的前面，我们可以使用一个大的分配。

我们创建 CStr 的功能:

```
char *c_str_new(char const *init_str); 
```

并且实现:

```
char *c_str_new(char const *init_str)
{
    int const init_str_length = strlen(init_str) + 1;

    CStrHeader *header = malloc(sizeof(CStrHeader) + (sizeof(char) * init_str_length * 2));

    header->length   = 0;
    header->capacity = init_str_length;

    char *ptr_to_string = (unsigned char *)header + sizeof(CStrHeader);
    strcpy(ptr_to_string, init_str);

    return ptr_to_string;
} 
```

我们要做的第一件事是计算`init_str`的长度，计算我们需要多少空间来存放这个字符串。接下来，我们“over”-为这个字符串分配一个有足够空间的`CStrHeader`对象。

现在已经分配了头，并用字符串的元数据填充了头。

下面是这个函数最有趣的部分:我们做了一些指针运算来跳过头部分并得到字符串。我们之所以将它转换成一个`unsigned char`指针，是因为它被有效地解释为字节。

因为 C 类型本质上是字节，所以 C 编译器需要知道如何移动字节光标。

每次对数组进行索引时，它都会将字节光标移动到该位置，方法是将数量乘以类型的大小。

现在回到我们的 CStr 库，我们将它转换为`unsigned char` s，并将字节光标移动`CStrHeader`的大小。

因为代表`unsigned char`的字节数实际上总是等于 1，所以我们可以像对待字节一样对待这个头。

最后，我们将`init_str`复制到`ptr_to_string`中并返回它。

释放字符串和它的头也很简单。

我们有一个名为`c_str_delete()`的函数，它接受一个 char 指针并删除头和字符串数据。

```
void c_str_delete(char *str)
{
    free((unsigned char *)str - sizeof(CStrHeader));
} 
```

首先，我们倒回到开始，然后自由。

## 结束语

这就是如何在 c 中创建一个方便易用的字符串库。您可以通过创建返回字符串长度和容量的函数来扩充它，方法是倒回到开头并访问结构字段成员。而且因为长度是预先计算好的，“计算”的是常数时间。只需返回长度。

在 C 中使用带有结构的头文件不仅仅是为了字符串库。如果您愿意，可以将它们用于 strecty 缓冲区。如果你想了解更多，就去搜索一下。它们或多或少就是我们所做的，除了它像 C++中的通用向量。