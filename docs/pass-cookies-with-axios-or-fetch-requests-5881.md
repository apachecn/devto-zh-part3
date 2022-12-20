# 用 axios 传递 cookies 或获取请求

> 原文：<https://dev.to/hugo__df/pass-cookies-with-axios-or-fetch-requests-5881>

当从客户端 JavaScript 发送请求时，默认情况下不传递 cookies。

> 默认情况下，fetch 不会从服务器发送或接收任何 cookies，从而导致未经验证的请求[https://developer . Mozilla . org/en-US/docs/Web/API/Fetch _ API/Using _ Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

我使用的两个 JavaScript HTTP 客户端是 [axios](https://github.com/axios/axios) ，一个“用于浏览器和 Node.js 的基于 Promise 的 HTTP 客户端”和 fetch API(参见 MDN 上的 [Fetch API)。](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

## 在 axios 中传递带有请求的 cookies

在 axios 中，为了能够传递 cookies，我们使用了`withCredentials: true`选项。

这意味着我们可以创建一个启用了`withCredentials`的新 axios 实例:

```
const transport = axios.create({
  withCredentials: true
})

transport
  .get('/cookie-auth-protected-route')
  .then(res => res.data)
  .catch(err => { /* not hit since no 401 */ }) 
```

也可以在请求选项中设置:

```
axios
  .get(
    '/cookie-auth-protected-route',
    { withCredentials: true }
  )
  .then(res => res.data)
  .catch(err => { /* not hit since no 401 */ }) 
```

或者覆盖全局默认值:

```
axios.defaults.withCredentials = true 
```

## 使用 fetch 传递带有请求的 cookies

与`fetch`等效的是在发送请求时设置`credentials: 'include'`或`credentials: 'same-origin'`选项:

```
fetch(
  '/cookie-auth-protected-route',
  { credentials: 'include' } // could also try 'same-origin'
).then(res => {
  if (res.ok) return res.json()
  // not hit since no 401
) 
```

[从亚历克斯那里免费下载高分辨率照片](https://unsplash.com/@worthyofelegance?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)