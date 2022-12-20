# 🦥反应过来.懒的没有默认出口

> 原文：<https://dev.to/iamandrewluca/react-lazy-without-default-export-4b65>

React v16.6.0 引入了`React.lazy`，允许在没有任何外部库的情况下进行代码拆分。

[https://reactjs.org/blog/2018/10/23/react-v-16-6.html](https://reactjs.org/blog/2018/10/23/react-v-16-6.html)

> React.lazy 函数允许您将动态导入作为常规组件呈现。
> 
> 之前:
> 
> ```
> import OtherComponent from './OtherComponent';
> 
> function MyComponent() {
>   return (
>     <div>
>       <OtherComponent />
>     </div>
>   );
> } 
> ```
> 
> 之后:
> 
> ```
> const OtherComponent = React.lazy(() => import('./OtherComponent'));
> 
> function MyComponent() {
>   return (
>     <div>
>       <OtherComponent />
>     </div>
>   );
> } 
> ```
> 
> [https://reactjs.org/docs/code-splitting.html#reactlazy](https://reactjs.org/docs/code-splitting.html#reactlazy)

虽然下面有一条信息

> `React.lazy`接受一个必须调用动态`import()`的函数。这必须返回一个`Promise`,它解析为一个带有包含 React 组件的`default`导出的模块。

这意味着您的`OtherComponent`应该以这种方式导出

```
export default function OtherComponent() {
  return (
    <div>OtherComponent</div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您将它导出为非默认的呢？

```
export function OtherComponent() {
  return (
    <div>OtherComponent</div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，当导入此组件
时，您必须稍微更改一下`import()`代码

```
const OtherComponent = React.lazy(
  () => import('./OtherComponent').then(module => ({ default: module.OtherComponent }))
); 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的只是链接由`import()`返回的`Promise`并添加默认导出。

请记住，用`React.lazy`导入的组件应该在`React.Suspense`中呈现

[https://reactjs.org/docs/code-splitting.html#suspense](https://reactjs.org/docs/code-splitting.html#suspense)

斯科特·凯利在 [Unsplash](https://unsplash.com/s/photos/sloth?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片