# 获取 ruby 方法块源代码

> 原文：<https://dev.to/siman/get-a-ruby-method-block-source-1if6>

## 概述

我尝试它。

`block_source.rb`(需要 RUBY_VERSION > = '2.6.0'

```
def inspector(ast:, &block)
  ast.children.each do |child|
    next unless child.instance_of?(RubyVM::AbstractSyntaxTree::Node)
    yield child
    inspector(ast: child, &block)
  end
end

def find_node(ast:, type:, lineno:)
  inspector(ast: ast) do |node|
    return node if node.type == type && node.first_lineno == lineno
  end

  nil
end

def extract_source(node:, source:)
  first_lineno = node.first_lineno - 1
  first_column = node.first_column
  last_lineno = node.last_lineno - 1
  last_column = node.last_column - 1

  if first_lineno == last_lineno
    source[first_lineno][first_column..last_column]
  else
    src = ' ' * first_column + source[first_lineno][first_column..]
    ((first_lineno + 1)...last_lineno).each do |lineno|
      src << source[lineno]
    end
    src << source[last_lineno][0..last_column]
  end
end

module Kernel
  # RUBY_VERSION >= '2.6.0'
  def block_source
    @file_specs ||= {}
    bl = caller_locations.last
    source = @file_specs.dig(bl.path, :source) || File.readlines(bl.path)
    @file_specs[bl.path] ||= { source: source, ast: RubyVM::AbstractSyntaxTree.parse(source.join) }
    node = find_node(
      ast: @file_specs.dig(bl.path, :ast),
      type: :ITER,
      lineno: bl.lineno
    )
    extract_source(node: node.children[1], source: source) if node
  end
end 
```

用法

```
require_relative 'block_source'

def foo
  pp block_source
end

foo { 'hello' }   #=> " { 'hello' }"
foo { |i| i * 3 } #=> " { |i| i * 3 }"
foo               #=> nil 
```

## 明细

首先，通过`bl = caller_locations.last`获取一个来电者信息。

```
bl = caller_locations.last 
```

第二，找到`ITER`节点。(`ITER`节点是带块的方法调用。)

```
node = find_node(
  ast: @file_specs.dig(bl.path, :ast),
  type: :ITER,
  lineno: bl.lineno
) 
```

`find_node`实施起来很简单。调用`inspector`并找到符合条件的节点。

```
def find_node(ast:, type:, lineno:)
  inspector(ast: ast) do |node|
    return node if node.type == type && node.first_lineno == lineno
  end

  nil
end 
```

最后，从`ITER`中提取块源。(`ITER`节点在第二个子元素中有块体。

```
extract_source(node: node.children[1], source: source) if node 
```

## 待办事宜

目前，不可能从`caller_locations`的信息中获得列号。所以，这个案子不成立。

```
foo { 'hello' }; bar { 'world' } 
```

```
" { 'hello' }"
" { 'hello' }" 
```