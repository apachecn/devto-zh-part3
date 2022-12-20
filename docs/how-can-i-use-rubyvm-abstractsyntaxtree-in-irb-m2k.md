# 如何在 irb 中使用 RubyVM::AbstractSyntaxTree.of？

> 原文：<https://dev.to/okinawarb/how-can-i-use-rubyvm-abstractsyntaxtree-in-irb-m2k>

将以下片段放到`~/.irbrc`中。

```
unless defined?(SCRIPT_LINES__)
  SCRIPT_LINES__ = {}
end

ast_happier = TracePoint.new(:call) do |tp|
  SCRIPT_LINES__['(irb)'] = tp.binding.local_variable_get(:statements).lines
end
ast_happier.enable(target: IRB::WorkSpace.instance_method(:evaluate)) 
```

成功了，耶！

```
% irb
irb(main):001:0> pp RubyVM::AbstractSyntaxTree.of(-> { puts :hi })
(SCOPE@1:35-1:48
 tbl: []
 args:
   (ARGS@1:35-1:35
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
 body: (FCALL@1:38-1:46 :puts (ARRAY@1:43-1:46 (LIT@1:43-1:46 :hi) nil)))
=> #<RubyVM::AbstractSyntaxTree::Node:SCOPE@1:35-1:48> 
```

## 但它是如何工作的呢？

`SCRIPT_LINES__`用于标准库`debug`。https://docs.ruby-lang.org/en/2.6.0/DEBUGGER__.html
T3

当然没有文档。

```
SCRIPT_LINES__ = {} unless defined? SCRIPT_LINES__ # :nodoc: 
```

[https://github.com/ruby/ruby/blob/v2_6_3/lib/debug.rb#L22](https://github.com/ruby/ruby/blob/v2_6_3/lib/debug.rb#L22)

在`ast.c`中，`SCRIPT_LINES__`也用于查找 proc 的源代码。

```
static VALUE
script_lines(VALUE path)
{
    VALUE hash, lines;
    ID script_lines;
    CONST_ID(script_lines, "SCRIPT_LINES__");
    if (!rb_const_defined_at(rb_cObject, script_lines)) return Qnil;
    hash = rb_const_get_at(rb_cObject, script_lines);
    if (!RB_TYPE_P(hash, T_HASH)) return Qnil;
    lines = rb_hash_lookup(hash, path);
    if (!RB_TYPE_P(lines, T_ARRAY)) return Qnil;
    return lines;
} 
```

[https://github.com/ruby/ruby/blob/v2_6_3/ast.c#L189-L201](https://github.com/ruby/ruby/blob/v2_6_3/ast.c#L189-L201)

```
static VALUE
rb_ast_s_of(VALUE module, VALUE body)
{
    VALUE path, node, lines;
    int node_id;
    const rb_iseq_t *iseq = NULL;

    if (rb_obj_is_proc(body)) {
        iseq = vm_proc_iseq(body);

        if (!rb_obj_is_iseq((VALUE)iseq)) {
            iseq = NULL;
        }
    }
    else {
        iseq = rb_method_iseq(body);
    }

    if (!iseq) return Qnil;

    path = rb_iseq_path(iseq);
    node_id = iseq->body->location.node_id;
    if (!NIL_P(lines = script_lines(path))) {
        node = rb_ast_parse_array(lines);
    }
    else if (RSTRING_LEN(path) == 2 && memcmp(RSTRING_PTR(path), "-e", 2) == 0) {
        node = rb_ast_parse_str(rb_e_script);
    }
    else {
        node = rb_ast_parse_file(path);
    }

    return node_find(node, node_id);
} 
```

[https://github.com/ruby/ruby/blob/v2_6_3/ast.c#L220-L253](https://github.com/ruby/ruby/blob/v2_6_3/ast.c#L220-L253)

在`RubyVM::AbstractSyntaxTree.of`期间，ruby 做到了

1.  从给定过程的`RubyVM::InstractionSequence`中读取文件位置。
2.  从某些来源读取整个源代码，例如`SCRIPT_LINES__`/由`-e`给出的真实文件/命令行输入
3.  再次解析整个源代码以创建 AST。
4.  从给定进程的`RubyVM::InstructionSequence`中读取节点 id。
5.  从 AST 中找到一个节点，该节点具有给定进程的相同节点 id。

如果解析同一个文件，ruby parser 会在那个位置为同一个对象给出相同的节点 id。这才是重点。

在评估 irb 输入之前，我使用`TracePoint`将源代码设置为`SCRIPT_LINES__`。

追踪点在这里工作。

```
 # Evaluate the given +statements+ within the  context of this workspace.
    def evaluate(context, statements, file = __FILE__, line = __LINE__)
      eval(statements, @binding, file, line)
    end 
```

[https://github . com/ruby/ruby/blob/v2 _ 6 _ 3/lib/IRB/workspace . Rb # L83-L86](https://github.com/ruby/ruby/blob/v2_6_3/lib/irb/workspace.rb#L83-L86)

好玩吗？