# 在被同时调用的函数中初始化一个常量变量

> 原文：<https://dev.to/parthchokshi/initialize-a-const-variable-inside-a-function-getting-called-simultaneously-1p4f>

### 在一个被同时调用的函数中初始化一个变量有什么好的做法，比如一个用于搜索的回调函数？

我现在有一个搜索功能，在 React 中每次按键都会调用这个功能。好做吗:

```
constructor() {
    this.searchResults = []
}

searchResult  = term => {
    this.searchResults = this.array.filter(arr => term == arr.something)
    this.setState({ searchResults: this.searchResults })
} 
```

或者

```
searchResult  = term => {
    const searchResults = this.array.filter(arr => term == arr.something)
    this.setState({ searchResults })
} 
```

const 变量会在每次按键后被垃圾回收吗？