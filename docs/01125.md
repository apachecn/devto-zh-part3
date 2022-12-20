# 在 TypeScript 中使用 styled-components + ReactNative 的平面列表

> 原文：<https://dev.to/acro5piano/use-styled-components-reactnative-s-flatlist-in-typescript-308e>

要用`styled-components` + `FlatList`，我们得这样写:

```
interface User {
  id: number
  name: string
}

const StyledFlatList = styled(FlatList as new () => FlatList<User>)`
  background-color: #f7f7f7;
` 
```

Enter fullscreen mode Exit fullscreen mode

[已编辑]
最后，我补充了我的变通方法。我希望有人能解决这个问题。

[https://github . com/styled-components/styled-components/issues/1803 # issue comment-497323287](https://github.com/styled-components/styled-components/issues/1803#issuecomment-497323287)
[/edited]

这是因为风格。FlatList 不支持通过键入样式化组件的定义。

参见:[https://github . com/styled-components/styled-components/issues/1294](https://github.com/styled-components/styled-components/issues/1294)

ref:[https://github . com/styled-components/styled-components/issues/1803 # issue comment-407332173](https://github.com/styled-components/styled-components/issues/1803#issuecomment-407332173)