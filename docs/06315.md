# 如何在 Swift 中轻松配置属性

> 原文：<https://dev.to/onmyway133/how-to-easily-configure-properties-in-swift-57i1>

这篇文章列出了我所知道的在 Swift 中轻松配置属性的许多方法

## 使用辅助方法

```
var label: UILabel!

override func viewDidLoad() {
  super.viewDidLoad()

  configureLabel()
}

func configureLabel() {
  label = UILabel()
  label.backgroundColor = UIColor.greenColor()
  view.addSubview(label)
} 
```

## 使用匿名功能

```
lazy var label: UILabel = { [weak self] in
  let label = UILabel()
  label.backgroundColor = UIColor.greenColor()
  return label
}() 
```

啊，顺便问一下，你知道吗

*   你不应该在 ViewController `deinit`中调用 access `label`，因为它是`lazy`而我们有`weak self`
*   增加你的编译时间

## @noescape 在 init 上配置块

第一次看到是在[https://github . com/AliSoftware/Dip/blob/develop/Sources/Dip . swift # L61](https://github.com/AliSoftware/Dip/blob/develop/Sources/Dip.swift#L61)

```
public init(@noescape configBlock: (DependencyContainer->()) = { _ in }) {
    configBlock(self)
} 
```

## 配置块为分机

这种[https://github.com/devxoul/Then](https://github.com/devxoul/Then)使得配置你的属性作为 NSObject
的扩展更加容易

```
extension Then where Self: AnyObject {

    public func then(@noescape block: Self -> Void) -> Self {
        block(self)
        return self
    }
} 
```

所以我们有

```
lazy var label: UILabel = UILabel().then { [weak self] in
    $0.backgroundColor = UIColor.greenColor()
} 
```

我们必须声明`label: UILabel`才能使用`[weak self]`

## init 不带扩展名

我尽量避免延伸，看完这个[http://nshipster.com/new-years-2016/](http://nshipster.com/new-years-2016/)

```
public func Init<Type>(value : Type, @noescape block: (object: Type) -> Void) -> Type
{
    block(object: value)
    return value
} 
```

我们可以像
一样使用它

```
lazy var label: UILabel = Init(UILabel()) { [weak self] in
  $0.backgroundColor = UIColor.greenColor()
} 
```

我们必须声明`label: UILabel`才能使用`[weak self]`

## 再次匿名功能

这个[https://gist.github.com/erica/4fa60524d9b71bfa9819](https://gist.github.com/erica/4fa60524d9b71bfa9819)让配置更简单

```
lazy var label: UILabel = { [weak self] in
  $0.backgroundColor = UIColor.greenColor()
  return $0
}(UILabel()) 
```