# d 和 C++:传递动态字符串

> 原文：<https://dev.to/fxcqz/d-and-c-passing-dynamic-strings-5ekj>

从 C++中获取动态创建的字符串的最简单方法是将 char pointer reference ( `char *&` ) out 参数传递给我们正在调用的函数。这允许我们在 C++中正确地 malloc，因为我们不知道 d 中 malloc 的字符串长度。

这个简单的例子创建了一个字符串，该字符串由整数 n 以内的所有数字组成，用空格分隔。

**ex.cpp**

```
#include <cstdio>  // for snprintf
#include <numeric> // for iota example
#include <sstream> // string builder
#include <vector> 
void numbers_upto(char*& out, int n) {
  std::vector<int> xs(n);
  std::iota(xs.begin(), xs.end(), 0);
  std::ostringstream buf;

  for(const int& x: xs) {
    buf << x << " ";
  }

  std::string str{buf.str()};
  size_t str_size = str.length();

  out = new char[str_size];
  snprintf(out, str_size, "%s", str.c_str());
} 
```

**ex.d**

D 端很简单，但是请注意，我们使用了一个范围保护来释放超出范围的字符串，以避免内存泄漏。D 中与`char *&`对应的类型是`ref char*`。

```
extern (C++) {
  void numbers_upto(ref char* s, int n);
}

void main() {
  import std.conv : to;
  import std.stdio : writeln;
  import core.stdc.stdlib : free;

  char* s;
  scope(exit) {
    // ensure s is not null before trying to free
    if (s !is null) {
      free(s);
    }
  }

  numbers_upto(s, 10);
  writeln(to!string(s));
} 
```

并且编译:

1.  `g++ -c -o cpp.o ex.cpp`
2.  `dmd ex.d cpp.o -L-lstdc++`
3.  `./ex`

它应该打印:

0 1 2 3 4 5 6 7 8 9

另外，我们可以编写一个包装器模板函数来调用这种类型的函数，并使用 D 契约来确保从 C++返回的字符串是有效的。

更新**ex . d**T2】

```
extern (C++) {
   void numbers_upto(ref char* s, int n);
}

void CheckedString(alias Fn, Args...)(ref char* s, Args args) 
out(; s !is null)
do {
  Fn(s, args);
}

void main() {
  import std.conv : to;
  import std.stdio : writeln;
  import core.stdc.stdlib : free;

  char* s;
  scope(exit) free(s);

  CheckedString!numbers_upto(s, 10);
  writeln(to!string(s));
} 
```

我们可以删除范围保护中的检查，因为如果契约断言失败，在检查范围之前，程序将被强制进入无效状态。

CheckedString 函数接受一个函数、一个`ref char*`和一组将传递给该函数的参数。out 部分是一个在函数完成后运行的契约。我们只是检查 ref 字符串是否不为空。

用同样的方式编译，你会得到和以前一样的结果。为了测试新的功能，只需在 C++代码中的`numbers_upto`函数结束之前取消`out`，就像这样:`out = nullptr;`和 D 契约应该如预期的那样失败。