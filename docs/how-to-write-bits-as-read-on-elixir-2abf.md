# 如何在 Elixir 上写读为 001 1101 的位串

> 原文：<https://dev.to/niku/how-to-write-bits-as-read-on-elixir-2abf>

更新:增加了使用`0b`和 [`:binary.encode_unsigned/1`](http://erlang.org/doc/man/binary.html#encode_unsigned-1) 的二进制处理。感谢[仙丹论坛](https://elixirforum.com/t/elixir-blog-posts/150/475?u=niku)。

# 我的动力

我想写一些类似于`010 1100 000 0010`(来自[协议缓冲区的编码样本](https://developers.google.com/protocol-buffers/docs/encoding))的关于测试专用药剂的内容。

请注意，我们必须在 erlang 或 elixir 上处理的大多数情况是[二进制，这意味着位串可以被 8](http://erlang.org/doc/programming_examples/bit_syntax.html) 整除。如果你想一边读一边写二进制，你可以用整数开始`0b`和 [`:binary.encode_unsigned/1`](http://erlang.org/doc/man/binary.html#encode_unsigned-1) ，比如:

```
:binary.encode_unsigned(0b1010_1100_1111_1111)
# => <<172, 255>> 
```

Enter fullscreen mode Exit fullscreen mode

但是，我想处理不能被 8 整除的位串，例如 3 位、7 位、17 位等等。`binary.encode_unsigned/1`与二进制对齐。

```
bit_size(:binary.encode_unsigned(0b101))
# => 8 
```

Enter fullscreen mode Exit fullscreen mode

在上述情况下，我想得到 3 位大小，但 8。所以我不能用`0b`和`:binary.encode_unsigned/1`来“写我读到的”。

我知道在我阅读的时候写比特串已经可以做到以下几点:

```
<<0::1, 1::1, 0::1, 1::1, 1::1, 0::1, 0::1, 0::1, 0::1, 0::1, 0::1, 0::1, 1::1, 0::1>>
# => <<88, 2::size(6)>> 
```

Enter fullscreen mode Exit fullscreen mode

这是正确的。因为，在整数中，一个 8 位的表示`010 1100 0`是`<<88>>`(可以勾选`Integer.digits(88, 2) # => [1, 0, 1, 1, 0, 0, 0]`，请注意标题`0` s 被去掉了)，剩下的`00 0010`是`<<2::size(6)>>`。

不过我感觉前者`<<0::1, 1::1, ...>>`挺啰嗦，后者`<<88, 2::size(6)>>`不直观。你不觉得吗？

我想在读取时写入位，例如:

```
010 1100 000 0010 
# => <<88, 2::size(6)>> 
```

Enter fullscreen mode Exit fullscreen mode

# 我的解决方案

[印记](https://elixir-lang.org/getting-started/sigils.html)是

> 该语言提供的处理文本表示的机制之一。

这就是我想要的。

第一次，我实现了如下功能:

```
# You can check the code on iex
defmodule BitsSigils do
  require Logger

  def sigil_b(term, _modifiers) do
    for <<c <- term>>,
      into: <<>> do
        case c do
          ^c when c in [?0, ?1] ->
            # It's a valid character as an element.
            <<c::1>>
          ^c when c in [?_, ?\s] ->
            # It's a valid character as a just placeholder.
            <<>>
          _ ->
            Logger.warn("Given unexpected sigil_b character, just ignored. The character was \"#{:binary.encode_unsigned(c)}\", its integer value was #{c}.")
            <<>>
        end
    end
  end
end

import BitsSigils
~b(_010 1100 _000 0010)
# => <<88, 2::size(6)>> 
```

Enter fullscreen mode Exit fullscreen mode

是的，看起来很好。但它在模式匹配中不作为左侧工作。

```
case <<88, 2::size(6)>> do
  ~b(_010 1100 _000 0010) ->
    true
  _ ->
    false
end

# ** (CompileError) iex:16: cannot invoke remote function BitsSigils.sigil_b/2 inside a match
#     (stdlib) lists.erl:1354: :lists.mapfoldl/3
#     (stdlib) lists.erl:1354: :lists.mapfoldl/3 
```

Enter fullscreen mode Exit fullscreen mode

最后，我将`sigil_b`实现为一个宏，如下所示:

```
# You can check the code on iex
defmodule BitsSigils do
  require Logger

  defmacro sigil_b({:<<>>, meta, [arg]}, modifiers) do
    result = do_sigil_b(arg, modifiers)
    {:<<>>, meta, [Macro.escape(result)]}
  end

  def do_sigil_b(term, _modifiers) do
    for <<c <- term>>,
      into: <<>> do
        case c do
          ^c when c in [?0, ?1] ->
            # It's a valid character as an element.
            <<c::1>>
          ^c when c in [?_, ?\s] ->
            # It's a valid character as a just placeholder.
            <<>>
          _ ->
            Logger.warn("Given unexpected sigil_b character, just ignored. The character was \"#{:binary.encode_unsigned(c)}\", its integer value was #{c}.")
            <<>>
        end
    end
  end
end

import BitsSigils
case <<88, 2::size(6)>> do
  ~b(_010 1100 _000 0010) ->
    true
  _ ->
    false
end
# => true 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

*   你必须处理的大多数情况是**二进制**，它是位串的特例。在这种情况下，您可以使用`0b`和 [`:binary.encode_unsigned/1`](http://erlang.org/doc/man/binary.html#encode_unsigned-1)
*   如果你想得到一些文本表示，你可以使用 [sigil](https://elixir-lang.org/getting-started/sigils.html) 。
*   如果你想在计算的左边使用 sigil，你必须使用宏而不是函数。