# 在 pry 中使用 RubyVM::AbstractSyntaxTree.of

> 原文：<https://dev.to/okinawarb/using-rubyvm-abstractsyntaxtree-of-in-pry-4cm3>

将以下片段放入`~/.pryrc`

```
unless defined?(SCRIPT_LINES__)
  SCRIPT_LINES__ = {}
end

Pry.config.hooks.add_hook(:before_eval, :"ast<3") do |code, _pry|
  SCRIPT_LINES__["(pry)"] = code.lines
  code
end 
```

成功了，耶！

```
% pry
[1] pry(main)> RubyVM::AbstractSyntaxTree.of(-> { puts :hi })
=> (SCOPE@1:32-1:45
 tbl: []
 args:
   (ARGS@1:32-1:32
    pre_num: 0
    pre_init: nil
    opt: nil
    first_post: nil
    post_num: 0
    post_init: nil
    rest: nil
    kw: nil
    kwrest: nil
    block: nil)
 body: (FCALL@1:35-1:43 :puts (ARRAY@1:40-1:43 (LIT@1:40-1:43 :hi) nil)))
[2] pry(main)> 
```

我还写了一个 irb 版本，包括`RubyVM::AbstractSyntaxTree.of`如何使用`SCRIPT_LINES__`。
[https://dev . to/okinawarb/how-can-I-use-rubyvm-abstractsyntaxtree-in-IR B- m2k](https://dev.to/okinawarb/how-can-i-use-rubyvm-abstractsyntaxtree-in-irb-m2k)

享受 AST！