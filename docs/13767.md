# Ruby 的模板设计模式。有什么理由不用模块而不用超类呢？

> 原文：<https://dev.to/briankephart/template-design-pattern-in-ruby-any-reason-not-to-use-a-module-instead-of-a-superclass-1gfg>

经典的模板设计模式使用继承来声明它的子类必须定义哪些方法，就像这样:

```
class Template
  ABSTRACT_METHOD_ERROR = 'You forgot to define that method.'

  def method_defined_in_class
    raise ABSTRACT_METHOD_ERROR
  end

  def method_not_defined_in_class
    raise ABSTRACT_METHOD_ERROR
  end
end

class ObjectFromTemplate < Template
  def method_defined_in_class
    'You remembered! No errors here.'
  end
end

obj = ObjectFromTemplate.new

obj.method_defined_in_class # => 'You remembered! No errors here.'

obj.method_not_defined_in_class # => RuntimeError (You forgot to define that method.) 
```

Enter fullscreen mode Exit fullscreen mode

我一直在想，是否有理由不为模板使用模块。

```
Module Template
  ABSTRACT_METHOD_ERROR = 'You forgot to define that method.'

  def method_defined_in_class
    raise ABSTRACT_METHOD_ERROR
  end

  def method_not_defined_in_class
    raise ABSTRACT_METHOD_ERROR
  end
end

class ObjectWithTemplate
  include Template

  def method_defined_in_class
    'You remembered! No errors here.'
  end
end

obj = ObjectWithTemplate.new

obj.method_defined_in_class # => 'You remembered! No errors here.'

obj.method_not_defined_in_class # => RuntimeError (You forgot to define that method.) 
```

Enter fullscreen mode Exit fullscreen mode

第二种方式对我来说似乎明显更好(由于所有的原因，组合通常比继承更受青睐)，但我从未见过这样做的。有什么想法或经验可以分享吗？