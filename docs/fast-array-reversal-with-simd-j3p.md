# SIMD 快速阵列反转！

> 原文：<https://dev.to/wunk/fast-array-reversal-with-simd-j3p>

|  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
|  | 连续的 | b WAP/rev | SSSC 3/霓虹灯 | AVX2 | AVX512 |
| 模式 | ![Serial](img/a50f9628b790258b6ccf1bd495378f9c.png) | ![bswap/rev](img/b634c14919c55d13da4a58ce5f4887a5.png) | ![SSSE3](img/a8dbc900645215e3e58ca8ed38108f17.png) | ![AVX2](img/2a70e61d24ce9135b1a5efeda31818b4.png) | ![AVX512](img/9ad82339179ce3a54c96404719918d5d.png) |
| 处理器 | 加速 |  |  |  |  |
| [i9-7900x](https://en.wikichip.org/wiki/intel/core_i9/i9-7900x) | x1 | x15.386 | x10.417 | x22.032 | x22.357 |
| [i3-6100](https://en.wikichip.org/wiki/intel/core_i3/i3-6100) | x1 | x15.8 | x10.5 | x16.053 | - |
| [i5-8600K](https://en.wikichip.org/wiki/intel/core_i5/i5-8600k) | x1 | x15.905 | x10.21 | x16.076 | - |
| [E5-2697 v4](https://en.wikichip.org/wiki/intel/core_i5/i5-8600k) | x1 | x16.701 | x15.716 | x19.141 | - |
| [BCM2837](https://en.wikipedia.org/wiki/Broadcom_Corporation#Raspberry_Pi) | x1 | x7.391 | x7.718 | - | - |

* * *

数组反转的实现通常包括交换数组的两端，并向下处理最中间的元素。C++具有类型意识，将数组元素视为对象，并将调用重载的类操作符，如`operator=`或可用的`copy by reference`构造函数。许多“交换”函数的实现会使用一个中间临时变量来进行交换，这需要至少两次调用对象的`operator=`和至少一次调用对象的`copy by reference`构造函数。其他一些新颖的算法在对被交换的数据作出一些假设(整数数据、寄存器绑定、无覆盖等)后使用 xor 交换技术。`std::swap`还允许一个类型使用`swap`重载，如果它与你的类型在同一个名称空间内，你应该在反转过程中向 C++的标准算法库公开你的重载方法

```
// Taken from http://en.cppreference.com/w/cpp/algorithm/reverse
// Example of using std::reverse
#include <vector>
#include <iostream>
#include <iterator>
#include <algorithm> 
int main()
{
    std::vector<int> v({1,2,3});
    std::reverse(std::begin(v), std::end(v));
    std::cout << v[0] << v[1] << v[2] << '\n';

    int a[] = {4, 5, 6, 7};
    std::reverse(std::begin(a), std::end(a));
    std::cout << a[0] << a[1] << a[2] << a[3] << '\n';
} 
```

请注意，对于奇数数量的元素，最中间的元素已经正好在它需要的位置，不需要移动。

[![](img/a50f9628b790258b6ccf1bd495378f9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pHDfgg6M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/Serial.gif)

如果`std::reverse`被调用到一个"**P**lain**O**l**D**ATA "(POD)类型，比如`std::uint8_t`(又名`unsigned char`)或一个普通的`struct`类型，那么编译器可以安全地假设您的数据没有任何特殊的赋值/复制覆盖需要担心，可以被当作原始字节处理。这种假设允许编译器将反转程序优化成更简单、更像`memcpy`的东西。

一个数组`std::uint8_t`上的`std::reverse`发出的 x86 通常如下所示。

```
 ; std::reverse for std::uint8_t
         0x000014a0 cmp rsi, rdi
     /=< 0x000014a3 je 0x14c5
     |   0x000014a5 sub rsi, 1
     |   0x000014a9 cmp rdi, rsi
    /==< 0x000014ac jae 0x14c5
   .---> 0x000014ae movzx eax, byte [rdi] ; Load two bytes, one from
   |||   0x000014b1 movzx edx, byte [rsi] ; each end.
   |||   0x000014b4 mov byte [rdi], dl    ; Write them at opposite
   |||   0x000014b6 mov byte [rsi], al    ; ends.
   |||   0x000014b8 add rdi, 1            ; Shift index at
   |||   0x000014bc sub rsi, 1            ; both ends "inward" toward the middle
   |||   0x000014c0 cmp rdi, rsi
   \===< 0x000014c3 jb 0x14ae
    \\-> 0x000014c5 ret 
```

在制作 qReverse 时，主接口实现遵循相同逻辑的模板化算法。
编译时的元素大小将被模板化，并发出一个适合这个精确大小的伪结构，以试图保持这个说明性的实现对于以字节为单位的*任何*大小的元素尽可能通用。通过将元素大小模板化，实现特定元素大小的专门化将容易得多，而所有其他非专门化的元素大小则退回到串行算法。
使用模板这样做只允许在编译时实例化适当的专门化，而不是在运行时将元素大小变量与可用实现列表进行比较。

```
template< std::size_t ElementSize >
inline void qReverse(void* Array, std::size_t Count)
{
    // An abstraction to treat the array elements as raw bytes
    struct ByteElement
    {
        std::uint8_t u8[ElementSize];
    };
    ByteElement* ArrayN = reinterpret_cast<ByteElement*>(Array);

    // If compiler adds any padding/alignment bytes(and some do) then assert out
    static_assert(
        sizeof(ByteElement) == ElementSize,
        "ByteElement is pad-aligned and does not match specified element size"
    );

    // Only iterate through half of the size of the Array
    for( std::size_t i = 0; i < Count / 2; ++i )
    {
        // Exchange the upper and lower element as we work our
        // way down to the middle from either end
        ByteElement Temp(ArrayN[i]);
        ArrayN[i] = ArrayN[Count - i - 1];
        ArrayN[Count - i - 1] = Temp;
    }
} 
```

从 gcc 发出的具有特定模板专门化的程序集:

> ```
> auto Reverse8 = qReverse<1>; 
> ```
> 
> ```
> void qReverse<1ul>(void*, unsigned long):
>   mov rcx, rsi
>   shr rcx
>   je .L1
>   lea rdx, [rsi-1]
>   lea rax, [rdi+rdx]
>   sub rdx, rcx
>   lea rdx, [rdi+rdx]
> .L3:
>   movzx ecx, BYTE PTR [rdi] ; Load bytes at each end
>   movzx esi, BYTE PTR [rax]
>   sub rax, 1                ; Move indexs "inwards"
>   add rdi, 1
>   mov BYTE PTR [rdi-1], sil ; Place them at the other end
>   mov BYTE PTR [rax+1], cl
>   cmp rax, rdx              ; Loop
>   jne .L3
> .L1:
>   rep ret 
> ```
> 
> ```
> auto Reverse16 = qReverse<2>; 
> ```
> 
> ```
> void qReverse<2ul>(void*, unsigned long):
>   mov rdx, rsi
>   shr rdx
>   je .L17
>   lea rax, [rdi-2+rsi*2]
>   add rdx, rdx
>   mov rsi, rax
>   sub rsi, rdx
> .L12:
>   movzx edx, WORD PTR [rdi] ; Same as above
>   movzx ecx, WORD PTR [rax]
>   sub rax, 2
>   add rdi, 2
>   mov WORD PTR [rdi-2], cx
>   mov WORD PTR [rax+2], dx
>   cmp rax, rsi
>   jne .L12
> .L17:
>   rep ret 
> ```
> 
> ```
> auto Reverse32 = qReverse<4>; 
> ```
> 
> ```
> void qReverse<4ul>(void*, unsigned long):
>   mov rdx, rsi
>   shr rdx
>   je .L25
>   lea rax, [rdi-4+rsi*4]
>   sal rdx, 2
>   mov rsi, rax
>   sub rsi, rdx
> .L20:
>   mov edx, DWORD PTR [rdi] ; Same as above
>   mov ecx, DWORD PTR [rax]
>   sub rax, 4
>   add rdi, 4
>   mov DWORD PTR [rdi-4], ecx
>   mov DWORD PTR [rax+4], edx
>   cmp rax, rsi
>   jne .L20
> .L25:
>   rep ret 
> ```
> 
> ```
> auto Reverse24 = qReverse<3>; 
> ```
> 
> ```
> void qReverse<3ul>(void*, unsigned long):
>   mov rdx, rsi
>   shr rdx
>   je .L25
>   lea rax, [rsi-3+rsi*2]
>   lea rdx, [rdx+rdx*2]
>   add rax, rdi
>   mov r8, rax
>   sub r8, rdx
> .L20:
>   movzx esi, WORD PTR [rax] ; Due to the element being 3 bytes
>   movzx ecx, WORD PTR [rdi] ; The arithmetic gets a little weird
>   sub rax, 3                ; But the principle is the same
>   movzx edx, BYTE PTR [rdi+2]
>   add rdi, 3
>   mov WORD PTR [rdi-3], si
>   movzx esi, BYTE PTR [rax+5]
>   mov WORD PTR [rsp-3], cx
>   mov BYTE PTR [rsp-1], dl
>   mov BYTE PTR [rdi-1], sil
>   mov WORD PTR [rax+3], cx
>   mov BYTE PTR [rax+5], dl
>   cmp rax, r8
>   jne .L20
> .L25:
>   rep ret 
> ```

从这里开始会变得更好！

这种“普通 ol 数据”的假设可以用于许多不同类型的数据。`struct`的大多数用法都被视为“数据包”,并且没有复制或交换的额外内存移动逻辑的限制，因为它们仅用于传达字节解释的结构。更明显的案例研究还可以是在一个 ASCII `string`中找到一个`chars`数组，或者是一行`uint32_t`像素数据。如果数组元素与寄存器大小(往往是 2 的幂)对齐，那么这些寄存器内的字节交换和混洗会特别有用。**从这一点开始，假设数据数组将被解释为这些“数据包”实例**，它们不涉及任何类型的`operator=`或`Foo (const Foo&)`类型的开销逻辑，因此数据可以被安全地严格解释为字节，就像`memcpy`一样。

市场上大部分运行 64 位或 32 位的机器，或者寄存器的大小很容易大于 1 字节(上面的动画中寄存器的大小为 4 字节，这是单个 32 位寄存器的大小)。据观察，这样可以加快速度，方法是加载一个完整的寄存器大小的字节块，在寄存器内翻转这个字节块，然后将其放在另一端！交换寄存器中的所有字节是网络中一个流行的操作，称为`endian swap`，x86 恰好有这样做的指令！

# bswap

`bswap`指令反转寄存器的单个字节，通常用于交换整数的`endian`以在`host`和`network`字节顺序之间交换(参见`htons`、`htonl`、`ntohs`、`ntohl`)。大多数 x86 编译器实现了汇编内部函数，您可以将其直接放入 C 或 C++代码中，让编译器直接发出`bswap`指令:

**MSVC:**

*   `_byteswap_uint64`
*   `_byteswap_ulong`
*   `_byteswap_ushort`

**GCC/Clang:**

*   `_builtin_bswap64`
*   `_builtin_bswap32`
*   `_builtin_bswap16`

x86 头`immintrin.h`还包括`_bswap`和`_bswap64`。否则，也可以使用更通用和可移植的实现，以使架构更通用。

```
inline std::uint64_t Swap64(std::uint64_t x)
{
    return (
        ((x & 0x00000000000000FF) << 56) |
        ((x & 0x000000000000FF00) << 40) |
        ((x & 0x0000000000FF0000) << 24) |
        ((x & 0x00000000FF000000) <<  8) |
        ((x & 0x000000FF00000000) >>  8) |
        ((x & 0x0000FF0000000000) >> 24) |
        ((x & 0x00FF000000000000) >> 40) |
        ((x & 0xFF00000000000000) >> 56)
    );
}

inline std::uint32_t Swap32(std::uint32_t x)
{
    return(
        ((x & 0x000000FF) << 24) |
        ((x & 0x0000FF00) <<  8) |
        ((x & 0x00FF0000) >>  8) |
        ((x & 0xFF000000) >> 24)
    );
}

inline std::uint16_t Swap16(std::uint16_t x)
{
    // This tends to emit a 16-bit `rol` or `ror` instruction
    return (
        ((x & 0x00FF) <<  8) |
        ((x & 0xFF00) >>  8)
    );
} 
```

大多数编译器能够检测到寄存器内字节序交换何时正在进行，并会自动发出`bswap`或针对目标架构的类似内在指令(ARM 架构有针对 **armv6** 或更新版本的`rev`指令)。还要注意的是，`bswap16`基本上只是一个 16 位旋转的 1 字节，也就是`rol`或`ror`指令。

x86_64 (gcc):

```
Swap64(unsigned long):
  mov rax, rdi
  bswap rax
  ret
Swap32(unsigned int):
  mov eax, edi
  bswap eax
  ret
Swap16(unsigned short):
  mov eax, edi
  rol ax, 8
  ret 
```

x86_64 (clang):

```
Swap64(unsigned long): # @Swap64(unsigned long)
  bswap rdi
  mov rax, rdi
  ret

Swap32(unsigned int): # @Swap32(unsigned int)
  bswap edi
  mov eax, edi
  ret

Swap16(unsigned short): # @Swap16(unsigned short)
  rol di, 8
  mov eax, edi
  ret 
```

ARM64 (gcc):

```
Swap64(unsigned long):
  rev x0, x0
  ret
Swap32(unsigned int):
  rev w0, w0
  ret
Swap16(unsigned short):
  rev16 w0, w0
  ret 
```

使用 32 位`bswap` s，该算法可以从任一端将一个 4 字节的*字节块*放入寄存器`bswap`中，然后将反转的*字节块*放在相对的两端。随着算法越来越接近中心，它可以使用更小的 16 位交换(也称为 16 位轮换)，如果它遇到 2 字节的块，并最终对任何剩余的块进行串行交换。

[![](img/33c408accf81aa966ccd365c00ff2eb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j4U1Hgs0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/Swap32.gif)

这当然可以在 64 位架构上扩展为 64 位`bswap`,允许更大的块一次反转。同样，首先尽可能多地进行 8 字节交换，然后进行 4 字节交换，然后是 2 字节交换，最后在需要时退回到串行 1 字节交换:

[![](img/b634c14919c55d13da4a58ce5f4887a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o4nuNaZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/Swap64.gif)

给定一个要反转的`11`字节数组(奇数，所以中间的字节保持不变)，将数组大小除以 2，得到要进行的*单元素*交换的数量(使用整数运算):

> `11 / 2 = 5`

因此`5`需要单元素串行交换来反转这个数组。现在有一种方法可以一次处理`4`个元素块，将这个结果`5`再除以`4`就可以知道需要多少个*四元素*交换。这一除法的余数是所有四元素互换用尽后仍然需要的串行互换数:

> `5 / 4 = 1`
> 
> `5 % 4 = 1`

所以只需要一个 4 字节的`bswap` -swap 和一个`naive` -swap 来完全反转一个 11 元素数组。现在可以添加 1 字节 qReverse 模板专门化了。

```
// Reverse an array of 1-byte elements(such as std::uint8_t)

// A specialization of the above implementation for 1-byte elements
// Does not call assignment or copy overloads
// Accelerated using - 64,32 and 16 bit bswap instructions
template<>
inline void qReverse<1>(void* Array, std::size_t Count)
{
    std::uint8_t* Array8 = reinterpret_cast<std::uint8_t*>(Array);
    std::size_t i = 0;

    // Using a new iteration variable "j" to illustrate that we know
    // the exact amount of times we have to use our chunk-swaps
    // BSWAP 64
    for( std::size_t j = i / 8; j < ((Count / 2) / 8); ++j )
    {
        // Get bswapped versions of our Upper and Lower 8-byte chunks
        std::uint64_t Lower = Swap64(
            *reinterpret_cast<std::uint64_t*>(&Array8[i])
        );
        std::uint64_t Upper = Swap64(
            *reinterpret_cast<std::uint64_t*>(&Array8[Count - i - 8])
        );

        // Place them at their swapped position
        *reinterpret_cast<std::uint64_t*>(&Array8[i]) = Upper;
        *reinterpret_cast<std::uint64_t*>(&Array8[Count - i - 8]) = Lower;

        // Eight elements at a time
        i += 8;
    }
    // BSWAP 32
    for( std::size_t j = i / 4; j < ((Count / 2) / 4); ++j )
    {
        // Get bswapped versions of our Upper and Lower 4-byte chunks
        std::uint32_t Lower = Swap32(
            *reinterpret_cast<std::uint32_t*>(&Array8[i])
        );
        std::uint32_t Upper = Swap32(
            *reinterpret_cast<std::uint32_t*>(&Array8[Count - i - 4])
        );

        // Place them at their swapped position
        *reinterpret_cast<std::uint32_t*>(&Array8[i]) = Upper;
        *reinterpret_cast<std::uint32_t*>(&Array8[Count - i - 4]) = Lower;

        // Four elements at a time
        i += 4;
    }
    // BSWAP 16
    for( std::size_t j = i / 2; j < ((Count / 2) / 2); ++j )
    {
        // Get bswapped versions of our Upper and Lower 4-byte chunks
        std::uint16_t Lower = Swap16(
            *reinterpret_cast<std::uint16_t*>(&Array8[i])
        );
        std::uint16_t Upper = Swap16(
            *reinterpret_cast<std::uint16_t*>(&Array8[Count - i - 2])
        );

        // Place them at their swapped position
        *reinterpret_cast<std::uint16_t*>(&Array8[i]) = Upper;
        *reinterpret_cast<std::uint16_t*>(&Array8[Count - i - 2]) = Lower;

        // Two elements at a time
        i += 2;
    }
    // Everything else that we can not do a bswap on, we swap normally
    // Naive swaps
    for( ; i < Count / 2; ++i )
    {
        // Exchange the upper and lower element as we work our
        // way down to the middle from either end
        std::uint8_t Temp(Array8[i]);
        Array8[i] = Array8[Count - i - 1];
        Array8[Count - i - 1] = Temp;
    }
} 
```

在某些架构和编译器上，可能会发出 [movebe](https://www.felixcloutier.com/x86/MOVBE.html) 指令，这是一条 CPU 指令，可以从内存位置读取或写入，并交换其字节序，所有这些都在一条指令中完成。英特尔处理器的`haswell`架构和 AMD 处理器的`excavator`架构支持该指令，如果您将`-march=native`与支持它的处理器一起使用，该指令将被自动使用。

现在是一些基准测试:在一个 *i3-6100* 和 *8gb 的 DDR4 ram* 上。在获得给定数组大小的平均执行时间之前，我在几个不同的数组大小上自动化了基准测试过程，给出了每个数组大小的`10,000`数组反转试验。使用 g++编译标志:`-m64 -Ofast -march=native`这些是当前 bswap `qreverse`算法的执行时间与`std::reverse`的比较结果:

| 元素计数 | 标准::反向 | 反面的 | 加速系数 |
| --- | --- | --- | --- |
| eight | 19 纳秒 | 19 纳秒 | *1.000* |
| 16 | 20 纳秒 | 19 纳秒 | **1.053** |
| Thirty-two | 24 纳秒 | 23 纳秒 | **1.043** |
| Sixty-four | 36 纳秒 | 23 纳秒 | **1.565** |
| One hundred and twenty-eight | 54 纳秒 | 21 纳秒 | **2.571** |
| Two hundred and fifty-six | 90 纳秒 | 22 纳秒 | **4.091** |
| Five hundred and twelve | 159 纳秒 | 26 纳秒 | **6.115** |
| One thousand and twenty-four | 298 纳秒 | 35 纳秒 | **8.514** |
| One hundred | 43 纳秒 | 21 纳秒 | **2.048** |
| One thousand | 290 纳秒 | 36 纳秒 | **8.056** |
| ten thousand | 2740 纳秒 | 191 纳秒 | **14.346** |
| One hundred thousand | 27511 纳秒 | 1739 纳秒 | **15.820** |
| One million | 279525 纳秒 | 24710 纳秒 | **11.312** |
| Fifty-nine | 32 纳秒 | 22 纳秒 | **1.455** |
| Seventy-nine | 43 纳秒 | 27 纳秒 | **1.593** |
| One hundred and seventy-three | 63 纳秒 | 29 纳秒 | **2.172** |
| Six thousand one hundred and thirty-three | 1680 纳秒 | 127 纳秒 | **13.228** |
| Ten thousand one hundred and seventy-seven | 2784 纳秒 | 190 纳秒 | 14.653 |
| Twenty-five thousand two hundred and fifty-three | 6864 纳秒 | 455 纳秒 | **15.086** |
| Thirty-one thousand three hundred and ninety-one | 8548 纳秒 | 564 纳秒 | **15.156** |
| Fifty thousand four hundred and thirty-two | 13897 纳秒 | 875 纳秒 | **15.882** |

因此全面加速到 ***x15.8！*** 之前对更大的数组大小稍有下降，这可能是由于高速缓存未命中累积了如此大量的数据。该算法延伸到潜在的大规模数组的任一端，这在某些点上导致缓存未命中的累积。仍然有一个*非常*大且显著的持续加速超过`std::reverse`，而不需要尝试做一些`_mm_prefetch`运算来让缓存运行。

# SIMD

`bswap`指令可以颠倒`2`、`4`或`8`字节的字节顺序，但几个 x86 扩展之后，现在可以通过使用`SIMD`一次交换`16`、`32`，甚至`64`字节的字节顺序。`SIMD`代表*单指令多数据*，允许仅使用单个指令并行操作多个数据通道。与自动反转寄存器中所有四个字节的`bswap`非常相似，`SIMD`提供了一个完整的算术指令集，允许使用一条指令同时并行处理多个数据实例。这些被操作的数据块往往被称为`vectors`数据。然后，多个字节的数据可以提升到`vector`寄存器中，以反转其顺序，并将其放在另一端，类似于`bswap`实施，但具有更大的块。

对算法的这些添加将跨越更高宽度的字节块，并将被附加到`bswap`加速交换循环链的上方，以确保最大的交换在较小的交换之前先耗尽。多年来，x86 架构经历了多代`SIMD`实现、改进和指令集:

*   `MMX` (1996)
*   `SSE` (1999 年)
*   `SSE2` (2001)
*   `SSE3` (2004)
*   `SSSE3` (2006)
*   `SSE4 a/1/2` (2006)
*   `AVX` (2008)
*   `AVX2` (2013)
*   `AVX512` (2015 年)

有些是为了兼容性而保留的(`MMX`)，有些是最近才出现的，难以捉摸的，或者对英特尔或 AMD 来说是如此*特定于供应商的*，以至于你可能不太可能拥有采用它的处理器(`SSE4a`)。有些是专门针对企业硬件的(如`AVX512`)，也不太可能出现在消费类硬件上。其他架构也可能有自己的 simd 实现，如 ARM 的 SIMD 协处理器`NEON`。

目前 steam 硬件调查显示 Steam 上所有 CPU 的 **94.42%** 具有`SSSE3`([store.steampowered.com/hwsurvey/](http://store.steampowered.com/hwsurvey/))。`SSSE3`是进入更高`SIMD`领域的第一步。`SSSE3`特别是由于它的`_mm_shuffle_epi8`指令允许处理器在我们的 128 位寄存器中相对容易地*混洗*字节，以说明这种实现。

# SSSE3

`SSE`代表“流式 SIMD 扩展”,而`SSSE3`代表“补充流式 SIMD 扩展 3 ”,这是*对`SSE`技术的第四次*迭代。SSE 引入了允许一些`128`位向量运算的寄存器。在 C 或 C++代码中，使用这些寄存器的意图是用诸如`__m128i`或`__m128d`之类的类型来表示的，这些类型告诉编译器，这些类型的*存储*的任何概念应该尽可能在 128 位`SSE`寄存器中找到它们的位置。像`_mm_add_epi8`这样的内部函数将两个`__m128i`相加，并将它们视为 8 位元素的*向量*，现在在 C 和 C++代码中也可以使用。`__m128i`和`__m128d`中的`i`和`d`分别用于通知 128 寄存器解释为`i` nteger 和`d`double 的意图。默认情况下`__m128`被假定为四个`floats`的向量。因为整数数据是被操作的，所以`__m128i`数据类型将被用作我们的数据表示，它提供对`_mm_shuffle_epi8`指令的访问。注意`SSSE3`需要 gcc 编译标志`-mssse3`。

现在起草一个`SSSE3`字节交换实现，并使用`SSSE3`创建一个模拟的 16 字节`bswap`。首先，在 C 或 C++代码中添加`#include <tmmintrin.h>`,将从`MMX`到`SSSE3`的每一个内部函数暴露给当前的源文件。然后，使用指令`_mm_loadu_si128`将`128`位的`load`与`u`对齐的`i`整数向量转换成`__m128i`变量。在硬件层面上，*未对齐的*数据和*对齐的*数据与内存硬件的接口略有不同，如果保证数据对齐，还可以提供一些进一步的轻微加速。无法假设传递给 qReverse 的指针是否对齐，因此将使用未对齐的内存访问。完成向量算术后，调用一个等价的`_mm_storeu_si128`，它将向量数据存储到一个未对齐的内存地址中。这个`SSSE3`实现将直接位于前面的`Swap64`实现之上，确保我们的算法在诉诸较小的块之前用尽尽可能多的较大的块:

```
#include <tmmintrin.h> 
...
for( std::size_t j = i / 16; j < ((Count / 2) / 16); ++j )
{
    const __m128i ShuffleRev = _mm_set_epi8(
        0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15
    );
    // Load 16 elements at once into one 16-byte register
    __m128i Lower = _mm_loadu_si128(
        reinterpret_cast<__m128i*>(&Array8[i])
    );
    __m128i Upper = _mm_loadu_si128(
        reinterpret_cast<__m128i*>(&Array8[Count - i - 16])
    );

    // Reverse the byte order of our 16-byte vectors
    Lower = _mm_shuffle_epi8(Lower, ShuffleRev);
    Upper = _mm_shuffle_epi8(Upper, ShuffleRev);

    // Place them at their swapped position
    _mm_storeu_si128(
        reinterpret_cast<__m128i*>(&Array8[i]),
        Upper
    );
    _mm_storeu_si128(
        reinterpret_cast<__m128i*>(&Array8[Count - i - 16]),
        Lower
    );

    // 16 elements at a time
    i += 16;
}
// Right above the Swap64 implementation...
for( std::size_t j = i / 8; j < ( (Count/2) / 8 ) ; ++j)
... 
```

这基本上使用`SSSE3`实现了一个增强的 16 字节`bswap`。它的核心是`_mm_shuffle_epi8`指令，该指令*根据在第二个参数中找到的字节索引向量来打乱*第一个参数中的向量，并返回这个新的*打乱的*向量。常量向量`ShuffleRev`使用`_mm_set_epi8`声明，每个字节都被设置为它应该从哪里获取字节的索引(从最低有效字节开始)。您可能会按照从 0 到 15 的升序来阅读它，但这实际上是以相反的顺序索引字节，从而给出完全相反的 16 位字节子数组。

[![](img/a8dbc900645215e3e58ca8ed38108f17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--il8sWDwf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/SSSE3.gif)

现在进行一些速度测试。

| 元素计数 | 标准::反向 | 反面的 | 加速系数 |
| --- | --- | --- | --- |
| eight | 19 纳秒 | 19 纳秒 | *1.000* |
| Sixteen | 22 纳秒 | 20 纳秒 | **1.100** |
| Thirty-two | 27 纳秒 | 20 纳秒 | **1.350** |
| Sixty-four | 37 纳秒 | 21 纳秒 | **1.762** |
| One hundred and twenty-eight | 55 纳秒 | 23 纳秒 | **2.391** |
| Two hundred and fifty-six | 91 纳秒 | 24 纳秒 | **3.792** |
| Five hundred and twelve | 158 纳秒 | 31 纳秒 | **5.097** |
| One thousand and twenty-four | 297 纳秒 | 42 纳秒 | **7.071** |
| One hundred | 43 纳秒 | 21 纳秒 | **2.048** |
| One thousand | 291 纳秒 | 42 纳秒 | **6.929** |
| ten thousand | 2743 纳秒 | 261 纳秒 | **10.510** |
| One hundred thousand | 27523 纳秒 | 2966 纳秒 | **9.280** |
| One million | 279812 纳秒 | 33832 纳秒 | **8.271** |
| Fifty-nine | 32 纳秒 | 21 纳秒 | **1.524** |
| Seventy-nine | 43 纳秒 | 24 纳秒 | **1.792** |
| One hundred and seventy-three | 62 纳秒 | 29 纳秒 | **2.138** |
| Six thousand one hundred and thirty-three | 1683 纳秒 | 185 纳秒 | **9.097** |
| Ten thousand one hundred and seventy-seven | 2787 纳秒 | 291 纳秒 | 9.577 |
| Twenty-five thousand two hundred and fifty-three | 6862 纳秒 | 712 纳秒 | 9.638 |
| Thirty-one thousand three hundred and ninety-one | 8546 纳秒 | 916 纳秒 | **9.330** |
| Fifty thousand four hundred and thirty-two | 13893 纳秒 | 1497 纳秒 | **9.281** |

加速比高达 ***x10.5*** ！...但这比达到了 ***x15.8*** 的`bswap`版本低？也许一些循环展开或一些预取可能有助于该算法与缓存配合良好。在这个实现中，可用的 8 个寄存器中只有两个被使用，因此有很大的改进空间(Todo)

# AVX2

该实现可以更进一步，使用`AVX/AVX2`扩展提供的更大的 256 位寄存器，一次反转 *32 字节块*。实现与`SSSE3`非常相似:使用`__m256i`类型将*未对齐的*数据加载到 256 位寄存器中。`AVX/AVX2`的问题在于`256-bit`寄存器实际上是两个独立的`128-bit` *通道*作为一个更大的`256-bit`寄存器并行操作，并且在功能上与`SSE`寄存器重叠，几乎是添加在`SSE`之上的额外抽象层。现在事情变得棘手了，没有一个`_mm256_shuffle_epi8`指令像你想象的那样工作。由于它只是在两个并行的 128 位通道上操作，`AVX/AVX2`指令引入了一个限制，其中一些跨通道算法需要特别的跨通道注意。一些指令将接受 256 位`AVX`寄存器，但实际上仅在 128 位通道上运行。这里的诀窍是，不要试图一次自动反转 256 位寄存器，而是反转两个 128 位通道内的字节，就像在`SSSE3`实现中一样洗牌两个 128 位寄存器，然后用*是*在`AVX/AVX2`中可用的任何跨通道算法反转两个 128 位通道本身。注意`AVX2`需要 gcc 编译标志`-mavx2`。

[_mm256_shuffle_epi8](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#techs=AVX,AVX2&text=shuffle&expand=4726) 是一条`AVX2`指令，它对 256 位寄存器的两个 128 位通道进行混洗，非常类似于`SSSE3`固有指令，因此可以先处理这个问题。

```
const __m256i ShuffleRev = _mm256_set_epi8(
    0, 1, 2, 3, 4, 5, 6, 7, 8, 9,10,11,12,13,14,15, // first 128-bit lane
    0, 1, 2, 3, 4, 5, 6, 7, 8, 9,10,11,12,13,14,15  // second 128-bit lane
);
// Load 32 elements at once into one 32-byte register
__m256i Lower = _mm256_loadu_si256(
    reinterpret_cast<__m256i*>(&Array8[i])
);
__m256i Upper = _mm256_loadu_si256(
    reinterpret_cast<__m256i*>(&Array8[Count - i - 32])
);

// Reverse each the bytes in each 128-bit lane
Lower = _mm256_shuffle_epi8(Lower,ShuffleRev);
Upper = _mm256_shuffle_epi8(Upper,ShuffleRev); 
```

因此，在大型 256 位寄存器中，两个 128 位通道现在反转，但现在 128 位通道本身必须反转。[_ mm 256 _ permute 2x 128 _ si 256](https://software.intel.com/en-us/node/524015)是另一个`AVX2`指令，它置换两个 256 位寄存器的 128 位通道:

[![](img/42baa4020a23b0ae3fae826b78b5445e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qdbeykrr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/_mm256_permute2x128_si256.jpg)

给定两个大的 256 位向量和一个 8 字节的立即值，它可以选择如何构建新的 256 位向量。为两个参数传递同一个变量，并从它们中“挑选”，就好像它们只是 16 字节元素的 2 元素数组一样，这可以模拟一次大的 128 位跨通道交换(可以想象成`__m128i SomeAVXRegister[2]`和进行`SomeAVXRegister[0]`或`SomeAVXRegister[1]`)。在某种程度上，这也是一个大的 128 位“旋转”,如果你能把它可视化的话。

```
...
for( std::size_t j = i / 32; j < ((Count / 2) / 32); ++j )
{
    const __m256i ShuffleRev = _mm256_set_epi8(
        0, 1, 2, 3, 4, 5, 6, 7, 8, 9,10,11,12,13,14,15,
        0, 1, 2, 3, 4, 5, 6, 7, 8, 9,10,11,12,13,14,15
    );
    // Load 32 elements at once into one 32-byte register
    __m256i Lower = _mm256_loadu_si256(
        reinterpret_cast<__m256i*>(&Array8[i])
    );
    __m256i Upper = _mm256_loadu_si256(
        reinterpret_cast<__m256i*>(&Array8[Count - i - 32])
    );

    // Reverse the bytes inside each of the two 16-byte lanes
    Lower = _mm256_shuffle_epi8(Lower,ShuffleRev);
    Upper = _mm256_shuffle_epi8(Upper,ShuffleRev);

    // Reverse the order of the 16-byte lanes
    Lower = _mm256_permute2x128_si256(Lower,Lower,1);
    Upper = _mm256_permute2x128_si256(Upper,Upper,1);

    // Place them at their swapped position
    _mm256_storeu_si256(
        reinterpret_cast<__m256i*>(&Array8[i]),
        Upper
    );
    _mm256_storeu_si256(
        reinterpret_cast<__m256i*>(&Array8[Count - i - 32]),
        Lower
    );

    // 32 elements at a time
    i += 32;
}
// Right above the SSSE3 implementation
... 
```

[![](img/2a70e61d24ce9135b1a5efeda31818b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uXNXdmUu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/AVX2.gif)

基准:

| 元素计数 | 标准::反向 | 反面的 | 加速系数 |
| --- | --- | --- | --- |
| eight | 19 纳秒 | 19 纳秒 | *1.000* |
| Sixteen | 21 纳秒 | 21 纳秒 | *1.000* |
| Thirty-two | 26 纳秒 | 20 纳秒 | **1.300** |
| Sixty-four | 37 纳秒 | 22 纳秒 | **1.682** |
| One hundred and twenty-eight | 54 纳秒 | 20 纳秒 | **2.700** |
| Two hundred and fifty-six | 91 纳秒 | 24 纳秒 | **3.792** |
| Five hundred and twelve | 159 纳秒 | 27 纳秒 | **5.889** |
| One thousand and twenty-four | 298 纳秒 | 36 纳秒 | **8.278** |
| One hundred | 45 纳秒 | 20 纳秒 | **2.250** |
| One thousand | 292 纳秒 | 36 纳秒 | **8.111** |
| ten thousand | 2739 纳秒 | 189 纳秒 | 14.492 |
| One hundred thousand | 27515 纳秒 | 1714 纳秒 | **16.053** |
| One million | 279701 纳秒 | 25417 纳秒 | **11.004** |
| Fifty-nine | 32 纳秒 | 21 纳秒 | **1.524** |
| Seventy-nine | 44 纳秒 | 25 纳秒 | **1.760** |
| One hundred and seventy-three | 63 纳秒 | 29 纳秒 | **2.172** |
| Six thousand one hundred and thirty-three | 1681 纳秒 | 127 纳秒 | **13.236** |
| Ten thousand one hundred and seventy-seven | 2782 纳秒 | 192 纳秒 | **14.490** |
| Twenty-five thousand two hundred and fifty-three | 6863 纳秒 | 449 纳秒 | **15.285** |
| Thirty-one thousand three hundred and ninety-one | 8545 纳秒 | 556 纳秒 | **15.369** |
| Fifty thousand four hundred and thirty-two | 13888 纳秒 | 875 纳秒 | 15.872 |

加速高达 ***x16.053*** ！

# AVX512

在商业世界中尤其罕见。即便如此，这种算法还能向前迈进一步，对大规模 512 位寄存器进行运算。这将允许一次交换`64`字节的数据。目前，`AVX512`指令集的 C 和 C++编译器实现充其量只是参差不齐。 [_mm512_shuffle_epi8](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#expand=4729,4753&text=_mm512_shuffle_epi8) 指令的好处是允许在具有 8 位索引的 512 位寄存器内对四个 128 通道寄存器进行混洗，尽管在 MSVC 或 GCC 中没有找到 [_mm512_set_epi8](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#=undefined&avx512techs=AVX512F&expand=4726,4038,4594,4594,4618,4618&text=_mm512_set_epi8) 的可靠实现。还有 [_mm512_set_epi32](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#=undefined&expand=4726,4038,4594,4594,4618,4618,4618,4611&text=_mm512_set_epi32&avx512techs=AVX512F) ，它需要生成`ShuffleRev`常量，以使用 32 位整数而不是 8 位整数。在初始的`_mm512_shuffle_epi8`之后，由于需要跨通道运算，四个通道仍然必须反转，因此需要额外的[_ mm 512 _ permutexvar _ epi 64](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#expand=4726,4594,4729,3972,4029,4029,3930,4729,3930,4029,4589,4029,4029,4047,4047&avx512techs=AVX512F&text=_mm512_permutexvar_epi64)来真正完成反转。这类似于上面的`AVX2`实现所必须做的。

`AVX512`不是单一的一组指令。`AVX512`具有不同的子集，这些子集可能针对特定处理器实施，也可能不实施。例如，有用于冲突检测的`AVX512CD`和用于指数和倒数指令的`AVX512ER`，尽管`AVX512` *的所有实现都要求*实现`AVX512F` (AVX-512 基础)。`_mm512_shuffle_epi8`是由`AVX512BW`子集执行的指令，它增加了 **B** yte 和 **W** ord 操作，而`_mm512_setepi32`和`_mm512_permutexvar_epi64`是`AVX512F`，因此`_mm512_shuffle_epi8`是这里涉及的唯一“拉伸”需求。`AVX512BW`目前由当前的 Skylake-X 处理器支持，并计划在未来的处理器中更普遍地实现。作为参考，当前的`AVX512`子集实现图。

[![](img/aa765e8f3b1b302d837aa847e5bf7c37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--apUsPlm9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/avx512-cpus.png)

在 GCC 中，`AVX512`的特定子集必须使用编译标志来启用:

| 子集 | 旗 |
| --- | --- |
| **基础** | `-mavx512f` |
| 预取 | `-mavx512pf` |
| 指数/倒数 | `-mavx512er` |
| 冲突检测 | `-mavx512cd` |
| 向量长度 | `-mavx512vl` |
| **字节和字** | `-mavx512bw` |
| 双字和四字 | `-mavx512dq` |
| *整数融合乘加* | `-mavx512ifma` |
| *向量字节操作* | `-mavx512vbmi` |

`_mm512_shuffle_epi8`并要求`-mavx512bw`标志在 gcc 中编译，其余的只要求`-mavx512f`。

```
// Could have done:
const __m512i ShuffleRev = _mm512_set_epi8(
    0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15,
    0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15,
    0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15,
    0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15
);
// but instead have to do the more awkward:
const __m512i ShuffleRev = _mm512_set_epi32(
    0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f,
    0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f,
    0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f,
    0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f
); 
```

完整的`AVX512`实现:

```
...
    for( std::size_t j = i / 64; j < ((Count / 2) / 64); ++j )
    {
        // Reverses the 16 bytes of the four  128-bit lanes in a 512-bit register
        const __m512i ShuffleRev8 = _mm512_set_epi32(
            0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f,
            0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f,
            0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f,
            0x00010203, 0x4050607, 0x8090a0b, 0xc0d0e0f
        );

        // Reverses the four 128-bit lanes of a 512-bit register
        const __m512i ShuffleRev64 = _mm512_set_epi64(
            1,0,3,2,5,4,7,6
        );

        // Load 64 elements at once into one 64-byte register
        __m512i Lower = _mm512_loadu_si512(
            reinterpret_cast<__m512i*>(&Array8[i])
        );
        __m512i Upper = _mm512_loadu_si512(
            reinterpret_cast<__m512i*>(&Array8[Count - i - 64])
        );

        // Reverse the byte order of each 128-bit lane
        Lower = _mm512_shuffle_epi8(Lower,ShuffleRev8);
        Upper = _mm512_shuffle_epi8(Upper,ShuffleRev8);

        // Reverse the four 128-bit lanes in the 512-bit register
        Lower = _mm512_permutexvar_epi64(ShuffleRev64,Lower);
        Upper = _mm512_permutexvar_epi64(ShuffleRev64,Upper);

        // Place them at their swapped position
        _mm512_storeu_si512(
            reinterpret_cast<__m512i*>(&Array8[i]),
            Upper
        );
        _mm512_storeu_si512(
            reinterpret_cast<__m512i*>(&Array8[Count - i - 64]),
            Lower
        );

        // 64 elements at a time
        i += 64;
    }
    // Above the AVX2 implementation
... 
```

[![](img/9ad82339179ce3a54c96404719918d5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--97FON4kF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/AVX512.gif)

由于`AVX512`目前在消费类硬件上非常罕见:[英特尔提供了一个仿真器](https://software.intel.com/en-us/articles/intel-software-development-emulator)，它可以提供一些验证，证明算法正确地反转了数组。然而，模拟器并不是一个合适的硬件基准的基础。在创建一个简单的测试程序来验证阵列已经被反转之后，可以通过仿真器运行并验证它:

`sde64 -mix -no_shared_libs -- ./Verify1 128`

```
Original:
0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 100 101 102 103 104 105 106 107 108 109 110 111 112 113 114 115 116 117 118 119 120 121 122 123 124 125 126 127
Reversed:
127 126 125 124 123 122 121 120 119 118 117 116 115 114 113 112 111 110 109 108 107 106 105 104 103 102 101 100 99 98 97 96 95 94 93 92 91 90 89 88 87 86 85 84 83 82 81 80 79 78 77 76 75 74 73 72 71 70 69 68 67 66 65 64 63 62 61 60 59 58 57 56 55 54 53 52 51 50 49 48 47 46 45 44 43 42 41 40 39 38 37 36 35 34 33 32 31 30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
[PASS] Array Reversed 
```

`-mix`将使软件开发仿真器审计程序的执行，并将其遇到的指令写入`sde-mix-out.txt`文件。默认情况下，这个文件很大，所以`-no_shared_libs`从报告中删除了对共享库(比如标准库)的审计。有了这个就可以检验`qreverse<1>`的执行总结:

```
# $dynamic-counts-for-function: void qReverse<1ul>(void*, unsigned long)  IMG: /qreverse/build/Verify1 at [0x4e1ffd40a0, 0x4e1ffd4e3e)   1.442%
#
# TID 0
#       opcode                 count
#
...
*isa-set-AVX512BW_512                                                  4
*isa-set-AVX512F_512                                                   6
...
*category-AVX512                                                       4
...
*avx512                                                               10
...
...
VMOVDQA64                                                              2  < Note these are called only twice which
VMOVDQU64                                                              2    makes sense given a 128-byte array
VMOVDQU8                                                               2    and only needing one AVX-512 swap
VPERMQ                                                                 2
VPSHUFB                                                                2
*total                                                                75 
```

不仅`AVX512BW`指令被验证已经运行并工作，而且`128`元素的整个反转总共只使用了`75`指令！

我最近购买了一台英特尔 i9-7900x `BX80673I97900X`，它具有很大一部分`AVX512`子集，能够为此实现提供一些实际的硬件基准。
这里的基准是使用 Visual Studio 2017 为 x64-Release 模式编译的。

| 元素计数 | 标准::反向 | 反面的 | 加速系数 |
| --- | --- | --- | --- |
| eight | 63 纳秒 | 59 纳秒 | **1.068** |
| Sixteen | 62 纳秒 | 61 纳秒 | **1.016** |
| Thirty-two | 74 纳秒 | 59 纳秒 | **1.254** |
| Sixty-four | 104 纳秒 | 61 纳秒 | **1.705** |
| One hundred and twenty-eight | 80 纳秒 | 18 纳秒 | **4.444** |
| Two hundred and fifty-six | 90 纳秒 | 20 纳秒 | **4.500** |
| Five hundred and twelve | 157 纳秒 | 22 纳秒 | **7.136** |
| One thousand and twenty-four | 276 纳秒 | 28 纳秒 | 9.857 |
| One hundred | 44 纳秒 | 20 纳秒 | **2.200** |
| One thousand | 269 纳秒 | 30 纳秒 | **8.967** |
| ten thousand | 2504 纳秒 | 112 纳秒 | 22.357 |
| One hundred thousand | 24222 纳秒 | 1368 纳秒 | **17.706** |
| One million | 236354 纳秒 | 21192 纳秒 | **11.153** |
| Fifty-nine | 33 纳秒 | 21 纳秒 | **1.571** |
| Seventy-nine | 40 纳秒 | 23 纳秒 | **1.739** |
| One hundred and seventy-three | 58 纳秒 | 29 纳秒 | **2.000** |
| Six thousand one hundred and thirty-three | 1438 纳秒 | 93 纳秒 | **15.462** |
| Ten thousand one hundred and seventy-seven | 2481 纳秒 | 147 纳秒 | **16.878** |
| Twenty-five thousand two hundred and fifty-three | 5794 纳秒 | 332 纳秒 | 17.452 |
| Thirty-one thousand three hundred and ninety-one | 7397 纳秒 | 420 纳秒 | **17.612** |
| Fifty thousand four hundred and thirty-two | 11915 纳秒 | 858 纳秒 | **13.887** |

加速到***22.357***的高原！

# 杂项

一旦我们从中间开始，最后只剩下类似于`4`“中间”的元素，那么我们只剩下一个`Swap32`元素来反转整个数组。如果我们一路走到中间，却以`5`元素结束，会怎么样？只要我们有`Swap16`，这实际上是不可能的。`5`中间的元素意味着`one middle element`和`two elements on either side`。我们的`for( std::size_t j = i/2; j < ( (Count/2) / 2)`将捕捉两边的两个元素`Swap16`，使我们只剩下中间的`1`元素，它可以留在反向数组中的正确位置(因为奇数数组中最中间的元素是我们的*支点*，不需要移动到任何地方)。

稍后，我们可以找到一种方法来加速我们的算法，让它有效地考虑这些枢纽案例，这样，在不得不使用天真交换的情况下，它甚至可以调用最后一个`Swap32`或甚至更大的，而不是在 4 字节案例的任何一半上调用两个`Swap16`。类似这样的事情可以完全消除天真交换的使用。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [温科罗](https://github.com/Wunkolo) / [逆转](https://github.com/Wunkolo/qreverse)

### 硬件加速 AoS 反转的小研究

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反过来 [![GitHub license](img/a5ee68a95d8f457ed498c54780ce4e61.png)](https://raw.githubusercontent.com/Wunkolo/qreverse/master/LICENSE)

qReverse 是一种架构加速的数组反转算法，旨在作为个人研究，利用 SIMD 设计一种快速 AoS 反转算法。

| 连续的 | b WAP/rev | SSSC 3/霓虹灯 | AVX2 | AVX512 |
| 模式 | [![Serial](img/a50f9628b790258b6ccf1bd495378f9c.png)](https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/Serial.gif) | [![bswap/rev](img/b634c14919c55d13da4a58ce5f4887a5.png)](https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/Swap64.gif) | [![SSSE3](img/a8dbc900645215e3e58ca8ed38108f17.png)](https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/SSSE3.gif) | [![AVX2](img/2a70e61d24ce9135b1a5efeda31818b4.png)](https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/AVX2.gif) | [![AVX512](img/9ad82339179ce3a54c96404719918d5d.png)](https://raw.githubusercontent.com/Wunkolo/qreverse/mastimg/AVX512.gif) |
| 处理器 | 加速 |
| [i9-7900x](https://en.wikichip.org/wiki/intel/core_i9/i9-7900x) | x1 | x15.386 | x10.417 | x22.032 | x22.357 |
| [i3-6100](https://en.wikichip.org/wiki/intel/core_i3/i3-6100) | x1 | x15.8 | x10.5 | x16.053 | - |
| [i5-8600K](https://en.wikichip.org/wiki/intel/core_i5/i5-8600k) | x1 | x15.905 | x10.21 | x16.076 | - |
| [E5-2697 v4](https://en.wikichip.org/wiki/intel/core_i5/i5-8600k) | x1 | x16.701 | x15.716 | x19.141 | - |
| [BCM2837](https://en.wikipedia.org/wiki/Broadcom_Corporation#Raspberry_Pi) | x1 | x7.391 | x7.718 | - | - |

* * *

数组反转的实现通常包括交换数组的两端，并向下处理最中间的元素。C++具有类型意识，将数组元素视为对象，并将调用重载的类操作符，如`operator=`或可用的`copy by reference`构造函数。许多“交换”函数的实现会使用一个中间临时变量来进行交换，这需要至少两次调用对象的`operator=`和至少一次调用对象的`copy by reference`构造函数。其他一些新颖的算法使用…

</article>

[View on GitHub](https://github.com/Wunkolo/qreverse)