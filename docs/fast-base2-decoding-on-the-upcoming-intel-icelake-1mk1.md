# 基于即将推出的英特尔 Icelake 的快速 base2 解码

> 原文：<https://dev.to/wunk/fast-base2-decoding-on-the-upcoming-intel-icelake-1mk1>

这是一篇关于一些预期代码的小文章，旨在对即将推出的英特尔 Icelake 架构进行最终测试和基准测试。

`pext`指令在 [BMI2](https://en.wikipedia.org/wiki/Bit_Manipulation_Instruction_Sets) 中是一条特别有用的指令，它允许程序员提供一个位屏蔽整数，其中的`1`位设置在感兴趣的位置，为此`pext`指令将 **ext** 提取 **p** 并行中的这些位，并根据最低有效位对它们进行压缩。

```
Given a bitmask and an input, pext will select the bits where-ever there is a
set bit in the mask, and compress them together to produce a new result.

|0000100000001111100000100001111100010000000010000001001000010000|  < Operand A
  ^      ^             ^       ^                ^      ^    ^   ^
|0100000010000000000000100000001000000000000000010000001000010001|  < Mask
                                   | Extract bits at mask
                                   V
|.1......0.............1.......1................0......1....1...0|
                                   | Compress into new 64-bit integer
                                   V
|0000000000000000000000000000000000000000000000000000000000110010|  < Result 
```

该指令对于诸如将 ascii[base 2](http://0x80.pl/notesen/2014-10-06-pext-convert-ascii-bin-to-num.html)T2[和十六进制](http://0x80.pl/notesen/2014-10-09-pext-convert-ascii-hex-to-num.html)转换回其原始数据字节的任务以及在[文本处理](http://0x80.pl/notesen/2019-01-05-avx512vbmi-remove-spaces.html#pext-based-algorithm-new)中的其他用途非常有用。

`pext`只有一个 32 位和 64 位变体，在通用寄存器中一次处理 4 或 8 个字节的数据
，因此它一次只能将
一个基 2 字节的数据(8 个 ascii 字符的`0`和`1`)处理回常规字节(由于端序问题，它还需要一个`bswap64`):

```
// Goes from ascii "00100101" to binary byte 0b00100101('a')
// the ascii string "00100101" is 8 bytes, so it fits perfectly
// with a 64-bit integer
inline std::uint8_t DecodeBase2Word( std::uint64_t BinAscii )
{
    const std::uint64_t CurInput = __builtin_bswap64(BinAscii);
    std::uint8_t Binary = 0;
#if defined(__BMI2__)
    // Much faster, or is it?
    Binary = _pext_u64(CurInput, 0x0101010101010101UL);
#else
    // Serial bit extraction
    std::uint64_t Mask = 0x0101010101010101UL;
    for( std::uint64_t CurBit = 1UL; Mask != 0; CurBit <<= 1 )
    {
        if( CurInput & Mask & -Mask )
        {
            Binary |= CurBit;
        }
        Mask &= (Mask - 1UL);
    }
#endif
    return Binary;
} 
```

* * *

[Icelake](https://en.wikipedia.org/wiki/AVX-512#CPUs_with_AVX-512) 引入了`AVX512`变种 [BITALG](https://en.wikipedia.org/wiki/AVX-512#New_instructions_in_AVX-512_VPOPCNTDQ_and_AVX-512_BITALG) 提供了发出[vpshubitqmb](https://github.com/HJLebbink/asm-dude/wiki/VPSHUFBITQMB)的本征
`_mm_bitshuffle_epi64_mask`。

该指令将遍历第二个操作数中的每个 64 位通道，
将其作为 8 个 8 位索引值(模 64)处理到另一个操作数的 64 位通道的位中，然后它将这 8 个选定的位压缩到 AVX512 屏蔽寄存器中的一个新的 8 位整数
。

虽然这不使用类似于`pext`位掩码，但它允许 64 位整数
被视为基本上是一个 64 位的查找表，以产生一个新的
8 位字节。不仅如此，它还在 SIMD 寄存器内的 64 位通道上运行，允许一次并行转换多达 8 个字节。
在 128 位 SSE 寄存器中一次可以转换 2 个字节，在
256 位 AVX 寄存器中一次可以转换 4 个字节，在 512 位寄存器中一次可以转换 8 个字节。

```
 3210987654321098765432109876543210987654321098765432109876543210
  666655555555554444444444333333333322222222221111111111
  -----------------------------------------------------------------
  0000100000001111100000100001111100010000000010000001001000010000| < Operand A
  |^      ^             ^       ^     +----------^      ^    ^   ^|
  ||      |             |       |     |       +---------+    |+--+|
  |+--+   +---+       +-+     +-+     |       |       +------+|   |
  |   |       |       |       |       |       |       |       |   |
  |---+-------+-------+-------+-------+-------+-------+-------+---|
  |  62   |  55   |  41   |  33   |  16   |   9   |   4   |   0   | < Operand B
  +---------------------------------------------------------------+
                                  | Get bits at index
                                  V
  +---------------------------------------------------------------+
  |   0   |   0   |   1   |   1   |   0   |   0   |   1   |   0   |
  +---------------------------------------------------------------+
                                  | Compress into new 8-bit integer
                                  V
                          +----------------+
                          |   0b00110010   |
                          +----------------+ 
```

有了这个，一个基本的贪婪算法就可以把不同宽度的 base2 ascii 码处理回原来的字节。`0`和`1`的 ascii 字节的最低有效位也是`0`和`1`。通过提取和压缩这些位，我们可以将 ascii 字节转换回它们原来的字节。

```
 // '0' : 0b00110000
// '1' : 0b00110001
//                ^ Extract and compress these bits
//                  the rest of he bits stay the same! (0x30)
//                  (assuming you've validated your input)

void Base2Decode(
    const std::uint64_t Input[], std::uint8_t Output[], std::size_t Length
)
{
    std::size_t i = 0;
    // 8 at a time
    for( std::size_t j = i/8 ; i < Length/8; ++j, i += 8 )
    {
        const __mmask64 Compressed = _mm512_bitshuffle_epi64_mask(
            _mm512_loadu_si512(reinterpret_cast<const __m512i*>(Input + i)),
            _mm512_set1_epi64(0x00'08'10'18'20'28'30'38)
        );
        _store_mask64(reinterpret_cast<__mmask64*>(Output + i), Compressed);
    }
    // 4 at a time
    for( std::size_t j = i/4 ; i < Length/4; ++j, i += 4 )
    {
        const __mmask32 Compressed = _mm256_bitshuffle_epi64_mask(
            _mm256_loadu_si256(reinterpret_cast<const __m256i*>(Input + i)),
            _mm256_set1_epi64x(0x00'08'10'18'20'28'30'38)
        );
        _store_mask32(reinterpret_cast<__mmask32*>(Output + i), Compressed);
    }
    // 2 at a time
    for( std::size_t j = i/2 ; i < Length/2; ++j, i += 2 )
    {
        const __mmask16 Compressed = _mm_bitshuffle_epi64_mask(
            _mm_loadu_si128(reinterpret_cast<const __m128i*>(Input + i)),
            _mm_set1_epi64x(0x00'08'10'18'20'28'30'38)
        );
        _store_mask16(reinterpret_cast<__mmask16*>(Output + i), Compressed);
    }
    // Serial(could probably just use the pext implementation here but I'm demonstrating bitshuffle_epi64 here)
    for( ; i < Length; ++i )
    {
        const __mmask16 Compressed = _mm_bitshuffle_epi64_mask(
            _mm_loadl_epi64(reinterpret_cast<const __m128i*>(Input + i)),
            _mm_set1_epi64x(0x00'08'10'18'20'28'30'38)
        );
        Output[i] = static_cast<std::uint8_t>(_cvtmask16_u32(Compressed));
    }
}

int main()
{
    // "Hello World!"
    const std::uint64_t* Input
    = (const std::uint64_t*)"010010000110010101101100011011000110111100100000010101110110111101110010011011000110010000100001";
    std::uint8_t Output[12] = {0};

    Base2Decode(Input, Output, 12);
    std::printf("Output: '%.12s'\n", Output);
} 
```

截至目前(太平洋时间 2019 年 5 月 30 日星期四下午 01:53:47)Icelake 还没有发布，所以我没有
的方法在硬件上实际测试它以获得一些性能数据
，但理论上它比*已经很快的* `pext`解码 base2-ascii 回二进制的方法快了 **x8** 倍！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [温克洛](https://github.com/Wunkolo) / [ base2](https://github.com/Wunkolo/base2)

### 一个类似于 GNU coreutil base64 的 base2 实现

<article class="markdown-body entry-content container-lg" itemprop="text">

# base 2[T1](https://raw.githubusercontent.com/Wunkolo/base2/master/LICENSE)

本着与 gnu coreutils 软件 [base64](https://www.gnu.org/software/coreutils/manual/html_node/base64-invocation.html) 相同的精神，`base2`将从文件或标准输入中读取的数据转换为 base2(二进制文本)编码形式。

因为我很无聊。

```
base2 - Wunkolo <wunkolo@gmail.com&gt
Usage: base2 [Options]... [File]
       base2 --decode [Options]... [File]
Options
  -h, --help            Display this help/usage information
  -d, --decode          Decode's incoming binary ascii into bytes
  -i, --ignore-garbage  When decoding, ignores non-ascii-binary `0`, `1` bytes
  -w, --wrap=Columns    Wrap encoded binary output within columns
                        Default is `76`. `0` Disables linewrapping 
```

* * *

编码:

```
% base2 <<< 'QWERTY'
01010001010101110100010101010010010101000101100100001010
% base2 --wrap=8 <<< 'QWERTY'
01010001                          # 'Q'
01010111                          # 'W'
01000101                          # 'E'
01010010                          # 'R'
01010100                          # 'T'
01011001                          # 'Y'
00001010                          # '\n' 
```

解码:

```
% base2 -d <<< '01010001010101110100010101010010010101000101100100001010'
QWERTY
% base2 -d <<< '010100010101
011101000garbage1010blah101001001010garbage1000101100100001010'
QWFz*J�B
% base2 -d -i <<< '010100010
101011101000garbage1010blah101001001010garbage1000101100100001010'
QWERTY 
```

* * *

我提到它的速度了吗:

[i3-6100](https://en.wikichip.org/wiki/intel/core_i3/i3-6100)

```
inxi -C
CPU:       Topology: Dual Core model:
```

…</article>

[View on GitHub](https://github.com/Wunkolo/base2)