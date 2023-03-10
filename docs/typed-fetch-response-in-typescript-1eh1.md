# TypeScript 中的类型化提取响应

> 原文：<https://dev.to/iamandrewluca/typed-fetch-response-in-typescript-1eh1>

目前 TypeScript `lib.dom.d.ts`没有实现类型化获取响应
[https://github . com/Microsoft/TypeScript/blob/master/lib/lib . DOM . d . ts # l 2230](https://github.com/Microsoft/TypeScript/blob/master/lib/lib.dom.d.ts#L2230)
下面你可以看到我们如何覆盖那个

```
interface TypedResponse<T = any> extends Response {
  /**
   * this will override `json` method from `Body` that is extended by `Response`
   * interface Body {
   *     json(): Promise<any>;
   * }
   */
  json<P = T>(): Promise<P>
}

interface Payload {
  id: number
} 
```

Enter fullscreen mode Exit fullscreen mode

第一种方法

```
function myFetch<T>(...args: any): Promise<TypedResponse<T>> {
  return fetch.apply(window, args)
}

myFetch<Payload>('/')
  .then(response => response.json())
  // here data will have Payload type
  .then(data => console.log(data.id))
// or
myFetch('/')
  .then(response => response.json<Payload>())
  // here data will have Payload type
  .then(data => console.log(data.id)) 
```

Enter fullscreen mode Exit fullscreen mode

第二种方法。我喜欢第一个

```
declare function fetch<T>(...args: any): Promise<TypedResponse<T>>

fetch<Payload>('/')
  .then(response => response.json())
  // here data will have Payload type
  .then(data => console.log(data.id))
// or
fetch('/')
  .then(response => response.json<Payload>())
  // here data will have Payload type
  .then(data => console.log(data.id)) 
```

Enter fullscreen mode Exit fullscreen mode

也许一些改革会对 https://github.com/Microsoft/TSJS-lib-generator/pull/622 和 T2 有所帮助