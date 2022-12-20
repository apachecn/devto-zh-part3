# Ruby 2.6 新特性:细化`#to_proc '

> 原文：<https://dev.to/okinawarb/ruby-26-new-feature-refine-toproc-2gn8>

> 细化发生在块传递。[功能# 14223]
> [https://github.com/ruby/ruby/blob/v2_6_0/NEWS](https://github.com/ruby/ruby/blob/v2_6_0/NEWS)

当您想将文字用作块的 DSL 时，这非常有用。
下面的例子细化了`Integer#to_proc`和`Array#to_proc`来分割值。

```
using Module.new {
  refine(Integer) do
    def to_proc
      -> o { o[self] }
    end
  end

  refine(Array) do
    def to_proc
      # Same as -> o { map(&o.method(:[]) }
      # https://dev.to/hanachin/ruby-27-new-feature-method-reference-operator-38l2
      -> o { map(&o.:[]) }
    end
  end
}

p "foo".match(/(a)(b)(c)/)&.then(&1) #=> nil
p "abc".match(/(a)(b)(c)/)&.then(&1) #=> "a"
p "abc".match(/(a)(b)(c)/)&.then(&[1]) #=> ["a"]
p "abc".match(/(a)(b)(c)/)&.then(&[1,2,3]) #=> ["a","b","c"] 
```