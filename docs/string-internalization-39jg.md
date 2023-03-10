# 字符串内部化

> 原文：<https://dev.to/noah11012/string-internalization-39jg>

许多现代的解释编程语言，如 Python 和 Lua，都有一个内置的特性，允许对字符串进行 O(1)比较。通常，比较两个字符串是一个 O(min(x，y))运算，对于巨大的字符串可能会导致性能下降。编译器和解释器可以利用这一点来提高检查关键字等的性能。这将有望让程序员专注于手头更重要的任务，如词法分析、解析和汇编指令或字节码的生成。

字符串内化是如何工作的？像所有其他与计算机和世界相关的问题一样，任何事情都有一个权衡。两个主要因素是效率和内存使用和消耗。某个问题的某些解决方案可能占用很少的内存，但与占用更多内存但效率更高的解决方案相比，可能会比较慢。我们可以有几种方法来内化一个字符串，但是我们将按照我一直在做的方法来做。

首先，我们需要一个数据结构来存储字符串本身，为了解决这个问题，我们将使用一个可伸缩的缓冲区。如果你不知道，它们是由西恩·布瑞特发明的，类似于 C++的 std::vector。因为本文讨论的是字符串内部化，所以我们不会关注弹性缓冲区的实现细节。

```
// If buffer is NULL, create it and push the item.
// If buffer is full, reallocate and push the item.
#define buffer_push(buffer, item) ... 
// Gets the length of a stretchy buffer.
#define buffer_length(buffer) ... 
```

当要比较两个内部化的字符串时，实际上比较的是什么？在我们的版本中，它是字符串的内存地址。如果两者相同，则字符串相同，如果不相同，则字符串不相同。

当我们内在化一个字符串时，我们将不得不搜索列表，并将每个条目与我们试图内在化的条目进行比较。这是我们付出的代价，但从长远来看是值得的，因为只要你愿意，你就可以保留这个内存地址。

```
/* Where all internalized strings will be stored. */
static char *inter_strings;

char const *string_internalize(char const *string)
{
    /* Search if the string already exists in the list. */
    int i = 0;
    while(i < buffer_length(inter_strings))
    {
        /* If found, return the internalized string. */
        if(string_compare(string, inter_strings))
            return inter_strings;
        ++i;
    }

    /* If not found, create internalized copy and return it. */
    char* new_string;
    int string_len = string_length(string);

    new_string = calloc(string_len + 1, sizeof(char));

    memory_copy(new_string, string, string_len);

    buffer_push(inter_strings, new_string);

    return new_string;
} 
```

这肯定是一个短的，但认为这是一些可能会有帮助的。