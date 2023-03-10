# 论 C 语言中的“联合”

> 原文：<https://dev.to/gefjon/on-union-in-c-2e6h>

这是我几个月前在/r/C_Programming 上写的对问题“有人能解释一下联合吗？”据我所知，没有人读过它，因为/r/C_Programming 基本上被抛弃了，大多数人都忽略了那种“当我报名参加一门使用 C 的大学课程时，我不知道我将进入什么样的环境！”呼救。

我认为这个问题非常有趣，因为在这个时代，像 Rust 的`enum`；哈斯克尔的[求和类型](https://en.wikipedia.org/wiki/Algebraic_data_type)；Java、C#和 C++中面向对象的子类化可以作为多态的更高级和相对狭窄的结构，但在 C 中如何使用联合(ab)并不总是显而易见的。

联合有两种常见的用例。一种是当你想存储一个可能是几种不同类型的值时，这种情况称为标记联合。举个常见的例子，考虑一个类型`Number`，它可以表示一个`int`或者一个`float` :

```
#include <stdio.h> 
enum NumberKind { FLOAT, INT };
struct Number {
  enum NumberKind kind;
  union {
    int i;
    float f;
  };
};

void output_number(struct Number * n) {
  switch (n->kind) {
  case INT:
     printf("The integer %d\n", n->i);
     break;
  case FLOAT:
    printf("The float %f\n", n->f);
    break;
  }
}

int main() {
  struct Number three = { .kind = INT, { .i = 3 } };
  struct Number two_point_five = { .kind = FLOAT, { .f = 2.5 } };

  output_number(&three);
  output_number(&two_point_five);

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

struct Number 的 union 部分将保存 int i 或 float f，enum NumberKind kind 字段告诉程序员它是哪一个。许多解释语言的实现使用标记联合(通常有迷人的优化，但那是另一个故事)来表示值——这就是为什么你不必在 Javascript 中编写像`int`、`float`或`struct Number`这样的类型。

和 C 语言中的大多数东西一样，这里有很多出错的空间；不管`n->kind`是什么，没有什么能阻止我阅读`n->i`，相信我，当我说我和其他每一个 C 程序员花了比我们想要调试的更多的时间。

不过，这种能力对于联合的另一个用途——类型双关——是至关重要的。Wikipedia 有一篇关于类型双关的很棒的文章，它将类型双关定义为“任何颠覆或绕过编程语言的类型系统的编程技术，以达到在正式语言的范围内难以或不可能达到的效果。”

这有点拗口，但关键是，在某一点上，你和类型系统会同意，类型双关是你作弊的方式。Rust 在标准库中有它，但是在 C 中我们被限制在指针强制转换或者联合中。

考虑用于[指针标记](https://en.wikipedia.org/wiki/Tagged_pointer)的代码。

```
#include <stdint.h>
#include <stdbool.h>
#include <assert.h>
#include <stdlib.h> 
union TaggedPointer {
  void * p;
  uintptr_t i;
};

// set the least significant bit of `ptr`
void * tag_a_pointer(void * ptr) {
  union TaggedPointer tagged = { .p = ptr };
  tagged.i |= 1;
  return tagged.p;
}

// true iff the least significant bit of `ptr` is set
bool has_low_bit_set(void * ptr) {
  union TaggedPointer tagged = { .p = ptr };
  return (tagged.i & 1) == 1;
}

int main() {
  void * some_pointer = malloc(16);

  assert(!has_low_bit_set(some_pointer));

  void * tagged = tag_a_pointer(some_pointer);
  assert(has_low_bit_set(tagged));

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你不熟悉的话，指针标记背后的思想是，由于[一些奇怪的废话](https://en.wikipedia.org/wiki/Data_structure_alignment)，指针的低位没有被使用，因此它们可以像标记联合中的标记一样被使用。

然而，要做到这一点，你需要能够像操作一个整数一样操作指针的位。因为 TaggedPointer.i 和 TaggedPointer.p 占用相同的内存块，所以改变其中一个(如在 tag_a_pointer 中)就会改变另一个。许多人认为这看起来比指针强制转换更干净，指针强制转换可能看起来像:

```
void * tag_a_pointer_but_with_pointer_casts(void * ptr) {
  uintptr_t i = *((uintptr_t *)(&ptr));
  i |= 1;
  return *((void **)(&i));
} 
```

Enter fullscreen mode Exit fullscreen mode

旁白:我在这篇文章中的两个例子相似的原因不是因为联合被限制在解释语言的标记中使用，而是因为我的业余爱好仅限于这个领域。这两种模式在语言中随处可见——我第一次遇到带标签的联合是在为 Xen hypervisor 挖掘 C 源代码的时候(这是我第一次接触 C 语言，但我只是个实习生，所以这并不重要)。我见过的第一个类型双关的例子是[fasteninversesquareroot](https://en.wikipedia.org/wiki/Fast_inverse_square_root)，这本身就是低级编程中的一次冒险。