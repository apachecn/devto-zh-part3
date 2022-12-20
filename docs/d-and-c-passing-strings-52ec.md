# d 和 C++:传递字符串

> 原文：<https://dev.to/fxcqz/d-and-c-passing-strings-52ec>

**ex.cpp**

```
#include <string>

const char* get_string() {
    return "Hello from C++!";
} 
```

**ex . d**T2】

```
extern (C++) {
  immutable(char)* get_string();
}

void main() {
  import std.conv : to;
  import std.stdio : writeln;

  string s = to!string(get_string());
  writeln(s);
} 
```

并且编译:

1.  `g++ -c -o cpp.o ex.cpp`
2.  `dmd ex.d cpp.o -L-lstdc++`
3.  `./ex`

它应该打印:

C++的你好！