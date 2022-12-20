# Ruby 异常处理

> 原文：<https://dev.to/k_penguin_sato/ruby-exception-handling-2098>

# 目录

*   [基本语法](#basic-syntax)
*   [相关方法](#relevant-methods)
*   [确保](#ensure)
*   [重试](#retry)
*   [救援修改器](#rescue-modifier)
*   [方法中的异常](#exception-in-methods)
*   [多个异常](#multiple-exceptions)
*   [自定义异常](#custom-exception)

# 基本语法

ruby 异常处理的基本语法如下。

```
begin 
  # Code that potentially raises an error 
rescue => e # variable
  # Execute if there was an error between begin and rescue.
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 举例

```
begin 
  raise 
rescue => e
  p e #=> RuntimeError
end 
```

Enter fullscreen mode Exit fullscreen mode

# 相关方法

| 方法 | 说明 |
| --- | --- |
| 对象#类 | 返回接收控件的类。 |
| 内核#提升 | 引发错误。 |
| 异常#回溯 | 返回错误的回溯。 |
| 异常#消息 | 返回错误信息。 |

#### 举例

```
begin 
  raise StandardError.new("Message")
rescue => e
  p e.class #=> StandardError
  p e.message #=> "Message"
  p e.backtrace #=> ["(repl):4:in `<main>'", "/run_dir/repl.rb:41:in `eval'", "/run_dir/repl.rb:41:in `run'", "/run_dir/repl.rb:57:in `handle_eval'", "/run_dir/repl.rb:170:in `start'", "/run_dir/repl.rb:177:in `start'", "/run_dir/repl.rb:181:in `<main>'"]
end 
```

Enter fullscreen mode Exit fullscreen mode

# 确保

无论是否存在错误，您都可以使用`ensure`运行某些程序

#### 举例

```
begin 
  "no Error"
rescue => e
  p e.message
ensure 
  p "Ensured" #=> Ensured
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 例 2

```
begin 
  raise StandardError.new('error')
rescue => e
  p e.message #=> error
ensure 
  p "Ensured" #=> Ensured
end 
```

Enter fullscreen mode Exit fullscreen mode

# 重试

字面上的执行。

#### 举例

```
file = ARGV[0]

begin
  # Open a file.
  io = File.open( file )
rescue
  # If there was an error during opening the file, this part gets executed.

  sleep( 10 )
  # Goes back to begin and re-try the execution.
  retry
end 
```

Enter fullscreen mode Exit fullscreen mode

# 救援修改器

你可以像用 Ruby 写`if ~ end`一样用一句话写`begin ~ rescue`。

#### 举例

```
raise "Error" rescue p "rescued" #=> rescued 
```

Enter fullscreen mode Exit fullscreen mode

# 方法中的异常

你不需要写`begin and end`来做方法中的异常处理。

#### 举例

```
def method
 raise 'error'
 rescue => e 
  p e.message #=> error
 ensure
  p 'ensured' #=> ensured
end
method #=> "error" 
```

Enter fullscreen mode Exit fullscreen mode

# 多个异常

对于每个相应的错误，您可以多次编写 rescue。

```
begin 
  rescue Exception1, Exception2 => e
  # For Exception1 or Exception2
  rescue Exception3 => e
  # For Exception3
  rescue => e
   # For other errors.
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 举例

```
begin 
  raise StandardError
  rescue StandardError, RangeError   
    p 'Standard or Ranage Error'
  rescue RuntimeError
    p 'Runtime Error'
  rescue => e
    p 'some other error'
end

#=> "Standard or Ranage Error" 
```

Enter fullscreen mode Exit fullscreen mode

# 自定义异常

只需按照下面的步骤。

(1)制作一个新的类

```
class CustomError < StandardError
end 
```

Enter fullscreen mode Exit fullscreen mode

(2)添加一条消息

```
class CustomError < StandardError
  def initialize(msg="My Error")
    super
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

(3)将自定义数据属性添加到您的异常中

```
class CustomError < StandardError
  attr_reader :atr
  def initialize(msg="My Error", atr="atrribute")
    @atr = atr
    super(msg)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 举例

```
class CustomError < StandardError
  attr_reader :atr

  def initialize(msg="My Error", atr="atrribute")
    @atr = atr
    super(msg)
  end
end

begin
  raise CustomError.new("CustomMessage", "Custom Attribute")
rescue => e
  p e.message #=> "CustomMessage"
  p e.atr  #=> "Custom Attribute"
end 
```

Enter fullscreen mode Exit fullscreen mode

# 参考文献

*   Ruby - Honeybadger 开发者博客中的自定义异常