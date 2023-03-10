# 具有嵌套属性的对象的生成器设计模式

> 原文：<https://dev.to/alialp/builder-design-pattern-for-objects-with-nested-properties-5g3c>

典型的[生成器设计模式](https://en.wikipedia.org/wiki/Builder_pattern)适用于像
这样的一维域对象

```
public class Data
{
    public string Id { get; set; }
    public DateTime DateTime { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

其对应的构建器类如下

```
public class DataBuilder
{
    private readonly Data _data;

    public DataBuilder()
    {
        _data = new Data();
    }

    public DataBuilder Id(string id)
    {
        this._data.Id = id;
        return this;
    }

    public DataBuilder Datetime(DateTime dateTime)
    {
        this._data.DateTime = dateTime; 
        return this;
    }

    public string Build()
    {
        return $"id: {_data.Id} date:{_data.DateTime}";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以可以这样用

```
var result = new DataBuilder()
            .Id("11")
            .Datetime(DateTime.Now)
            .Build(); 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们的域对象由其他域对象组成，就像这样

```
public class Data
{
    public string Id { get; set; }
    public DateTime DateTime { get; set; }
    public Nested Nested { get; set; }
}

public class Nested
{
    public string Name { get; set; }
    public string Surname { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，构建器模式的使用之美将会受到某种程度的损害

```
var nested = new Nested()
{
    Name="Ali",
    Surname="Tabryzy"
};

var result = new DataBuilder()
    .Id("11")
    .Nested(nested)
    .Datetime(DateTime.Now)
    .Build(); 
```

Enter fullscreen mode Exit fullscreen mode

为了保持构建器模式的美观，我们可以这样实现它

```
public class DataBuilder
{
    private readonly Data _data;
    public readonly NestedBuilder Nested;
    private Nested _nested;

    public class NestedBuilder
    {
        private readonly DataBuilder _dataBuilder;

        public NestedBuilder(DataBuilder dataBuilder)
        {
            _dataBuilder = dataBuilder;
        }

        public DataBuilder Name(string name)
        {
            if (_dataBuilder._nested == null)
                _dataBuilder._nested = new Nested();
            _dataBuilder._nested.Name = name;
            return _dataBuilder;
        }

        public DataBuilder Surname(string surname)
        {
            if (_dataBuilder._nested == null)
                _dataBuilder._nested = new Nested();
            _dataBuilder._nested.Surname = surname;
            return _dataBuilder;
        }
    }

    public DataBuilder()
    {
        _data = new Data();
        Nested = new NestedBuilder(this);
    }

    public DataBuilder Datetime(DateTime dateTime)
    {
        this._data.DateTime = dateTime; 
        return this;
    }
    public DataBuilder Id(string id)
    {
        this._data.Id = id;
        return this;
    }

    public string Build()
    {
        return $"id: {_data.Id} name:" + 
            $"{_nested.Name} surname: {_nested.Surname} date:{_data.DateTime}";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种用法会像一维定义域对象一样漂亮，如下

```
var result = new DataBuilder()
    .Id("11")
    .Nested.Name("ali")
    .Nested.Surname("Tabryzy")
    .Datetime(DateTime.Now)
    .Build(); 
```

Enter fullscreen mode Exit fullscreen mode

快乐编码:)