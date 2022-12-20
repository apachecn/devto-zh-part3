# 第一个帖子，动机，一些注释

> 原文：<https://dev.to/ijens1/first-post-motivations-some-notes-1khe>

# 第一帖

你好。

我花了一上午寻找这样的东西，我很高兴找到了。以前从未写过任何东西，所以这是我的第一篇文章。

# 动机

我想记录我每天正在做的事情和我正在学习的东西。这些就是我的动机。

# 备注

至于笔记，我将直接进入我现在正在学习的内容，并回顾 Scott Meyers 的《有效的现代 C++》中的一些内容。请记住，由于他的描述更不正式，我的描述也会更不正式，我很可能会在措辞上非常接近他在文本中的描述。也请注意，我将按时间顺序阅读这些文章，所以我可能会回来对一些帖子进行编辑，并添加关于我后来学到的新东西的笔记。

为了让这篇评论对我有效，我将尽可能多地根据记忆和理解来写，然后在我引用文本时使用以下格式在博客中明确说明:

**RSB** *内容我不记得了，完全来源于正文* **RSE**

和

**RCB** *我不确定需要用文字确认的内容*
T5】RCE

这些“引用括号”之外的所有内容完全来自内存。

我们走吧。

### 第一个评审项目

模板类型扣减的工作原理。有三种情况
可以让你推断出模板化函数中的`T`是什么:

```
template <typename T> void f(ParamType t) {}
...
f(expr); 
```

每个案例都依赖于`ParamType`的形式:

1.  `ParamType`是引用或指针 **RSB** 而不是通用引用 **RSE** 。
2.  `ParamType`RCB**T2**RCE**是万能参照物吗？**
3.  `ParamType`既不是引用也不是指针。

#### 案例 1

在这种情况下，请遵循以下步骤:

1.  如果`expr`是引用，忽略引用部分。
2.  使用经典的检查技术(Meyers 称之为模式匹配)来计算出 T 应该是什么。

##### 举例

```
template <typename T> void f(T& t) {}
...
int x = 0;
int& r_x = x;
const int& rc_x = x;

f(x); // Since x is not a reference, nothing to ignore,
      // By inspection, we can determine that T must be int

f(r_x); // Since r_x is a reference, we ignore the reference part,
        // Then by inspection, we again determine that T must be int

f(rc_x); // Since rc_x is a reference, we ignore the reference part,
         // Then by inspection, we determine that T must be const int 
```

#### 案例二

在这种情况下，请遵循以下准则:

1.  如果`expr`是左值，那么`ParamType`和`T`都是左值引用类型。注意，这对他们来说很奇怪**和**都是左值引用。
2.  如果`expr`是一个右值，那么遵循情况 1 的规则

##### 举例

```
template <typename T> void f(T&& t) {}
...
int x = 0;
int& r_x = x;
const int& rc_x = x;

f(x); // x is an lvalue here, so T is int& and ParamType is also int&

f(r_x); // r_x is an lvalue, so T and ParamType are int&

f(rc_x); // rc_x is an lvalue, so T and ParamType are const int&

f(27); // 27 is an rvalue, so we follow case 1 rules to determine that T should
       // be int and so ParamType is RSB int&& RSE. 
```

#### 案例三

在这种情况下，我们遵循以下步骤:

1.  忽略引用。
2.  **RCB** 忽略任何简历限定符 **RCE** 。

##### 举例

```
template <typename T> void f(T t) {}
...
int x = 0;
const int c_x = x;
const int& rc_x = x;

f(x); // T and ParamType are int

f(c_x); // Ignore the const, so T and ParamType are int

f(rc_x); // Ignore the reference, then the const, so T and ParamType are int. 
```

现在，我们已经完成了案例，但是 Meyers 继续写一些其他的笔记
关于数组衰减为指针和函数衰减为函数
指针。

##### 数组衰减为指针

**RSB**
迈耶斯注意到，我们通常认为指针和数组
是可以互换的，根据下面的例子:

```
// Example 1
void func(int param[]);

// The above declaration is actually equivalent to this declaration using a pointer
void func(int *param);

// Example 2
const int important_numbers[] = {1, 25, 125};

const int * ptr_to_important_numbers = important_numbers; // array decays to pointer

// Example 3 (uses important_numbers declared in previous example)
template <typename T> void func(T t) {}

func(important_numbers); // T is deduced to be const int * here 
```

然而，如果模板中参数类型的形式是引用，`T`将
实际上被推导为正确类型的数组，其中类型也
包括数组的大小。因此，如果我们将上面的示例 3 修改为

```
template <typename T> void func(T& t) {}

func(important_numbers); 
```

`T`将被成功地推导为`const int (&)[3]`。这允许你
在编译时推断数组的大小，非常有趣:

```
template <typename T, int N> constexpr std::size_t sizeOfArray(T (&)[N])
noexcept {
  return N;
} 
```

##### 函数衰减为函数指针

他还注意到函数也会发生类似的事情:

```
template <typename T> void f1(T t) {}

template <typename T> void f2(T& t) {}

void myFunc(const char*, int); // myFunc has type void(const char*, int)

f1(myFunc); // T is deduced to type void (*)(const char*, int)
f2(myFunc); // T is deduced to type void (&)(const char*, int) 
```