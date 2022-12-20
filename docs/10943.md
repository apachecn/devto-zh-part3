# JavaScript 中的 Object.keys()

> 原文：<https://dev.to/olegchursin/objectkeys-in-javascript-4g6>

关于您可以用 JavaScript 的`Object.keys()`方法做什么的快速说明。

我们将使用它将一个`Object` ( `dictionary`如果你喜欢一些术语清晰，或者`hash`如果你来自 Ruby 的世界)转换成一个具有三个不同输出的`Array`:

1)用重新分配的键-值对创建一个对象数组，
2)创建一个键数组，
3)创建一个值数组。

我们开始吧。我们最初的对象是几个美国联邦假日，名称作为键，日期作为值:

```
const holidays = {   
  NYE: '2018-01-01',   
  XMAS: '2018-12-25' 
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 重新定义了键值对的对象数组:

```
const holidaysArray = Object.keys(holidays).map(key =>    
  ({
    name: key,
    date: holidays[key] 
  }) 
)

// => [ 
//      { name: 'NYE', date: '2018-01-01' },
//      { name: 'XMAS', date: '2018-12-25' }
//    ] 
```

Enter fullscreen mode Exit fullscreen mode

#### 键的数组:

```
const keysArr = Object.keys(holidays).map(key => {
  return key;
}

// => [ 'NYE', 'XMAS' ] 
```

Enter fullscreen mode Exit fullscreen mode

#### 数组的值:

```
const valuesArr = Object.keys(holidays).map(key => {
  return holidays[key];
}

// => [ '2018-01-01', '2018-12-25' ] 
```

Enter fullscreen mode Exit fullscreen mode

保持简洁明了。下次见。