# Ruby:从 string 或 nil 中获取布尔值

> 原文：<https://dev.to/sakko/getting-boolean-from-string-or-nil-2fio>

有时候你会碰到

```
nil == false
 => false
'true' == true
 => false
'false' == false
 => false 
```

以下是我对这个问题的解决方案。(不确定这是不是最好的方式，但我喜欢)

注意:如果您使用的是 rails，请在`config/initializers`文件夹中创建一个名为`extend_string.rb`的文件。然后添加下面的代码。

> 或者在启动你的 ruby 应用程序之前把它导入某个地方。

```
class NilClass
  def self.to_bool
    false
  end
  def to_bool
    false
  end
end

class String
  def to_bool
      return true if self.downcase == "true"
      return false if self.downcase == "false"
      return nil
  end
end 
```

现在您的代码中已经有了这个，您可以运行

```
nil.to_bool == false
 => true
'true'.to_bool == true
 => true
'false'.to_bool == false
 => true
'TRUE'.to_bool == true
 => true
'FaLSE'.to_bool == false
 => true 
```

如果你有任何其他方式做到这一点，请让我知道，这样我就可以改善我的代码。

感谢:D