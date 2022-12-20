# Axios 拦截器使用 Axios 拦截器处理全局 HTTP 请求和响应

> 原文：<https://dev.to/teroauralinna/global-http-request-and-response-handling-with-the-axios-interceptor-30ae>

这是一个简短的例子，展示了如何捕捉所有的 [Axios](https://github.com/axios/axios) HTTP 请求、响应和错误。捕捉是通过称为[拦截器](https://github.com/axios/axios#interceptors)的 Axios 特性实现的。有可能在发送请求之前捕获所有请求并修改它们。此外，响应和错误可以被全局捕获。例如，当您希望在发送请求之前修改请求头，或者您正在实现某种全局错误处理系统时，拦截器就非常有用。

当然，对于真实的用例，以同样的方式处理所有的请求和响应可能太笼统了。这就是为什么我们还将为每个 HTTP 请求提供一个配置选项来禁用全局处理。

## 演示可在 [CodePen](https://codepen.io/teroauralinna/pen/vPvKWe) 获得

在演示中，我使用了 [VanillaToasts](http://alexkvazos.github.io/VanillaToasts/) 库来显示 HTTP 请求完成时的通知。

[https://codepen.io/teroauralinna/embed/vPvKWe?height=600&default-tab=result&embed-version=2](https://codepen.io/teroauralinna/embed/vPvKWe?height=600&default-tab=result&embed-version=2)

## 安装 Axios

按照说明从[安装 Axios](https://github.com/axios/axios#installing) 开始。

创建一个新的[实例](https://github.com/axios/axios#creating-an-instance)，并添加您可能需要使用的任何全局配置选项。

```
const axiosInstance = axios.create({
  baseURL: 'https://...'
}) 
```

## 使 HTTP 请求处理程序可配置

添加`isHandlerEnabled`函数，它将检查是否应该使用全局处理程序。这里还可以实现额外的定制逻辑。例如，只为某些 HTTP 响应代码启用处理程序。

```
const isHandlerEnabled = (config={}) => {
  return config.hasOwnProperty('handlerEnabled') && !config.handlerEnabled ? 
    false : true
} 
```

现在，如果我们愿意，我们以后可以禁用单个 HTTP 调用的处理程序。

```
await axiosInstance.get('/v2/api-endpoint', { handlerEnabled: false }) 
```

## Axios 请求拦截器

### 添加请求处理程序

请求处理程序的一个常见用例是修改或添加新的 HTTP 头。例如，身份验证令牌可以注入到所有请求中。

```
const requestHandler = (request) => {
  if (isHandlerEnabled(request)) {
    // Modify request here
    request.headers['X-CodePen'] = 'https://codepen.io/teroauralinna/full/vPvKWe'
  }
  return request
} 
```

### 启用请求拦截器

```
axiosInstance.interceptors.request.use(
  request => requestHandler(request)
) 
```

## Axios 响应和错误拦截器

### 添加响应处理程序

```
const errorHandler = (error) => {
  if (isHandlerEnabled(error.config)) {
    // Handle errors
  }
  return Promise.reject({ ...error })
}

const successHandler = (response) => {
  if (isHandlerEnabled(response.config)) {
    // Handle responses
  }
  return response
} 
```

### 启用拦截器

```
axiosInstance.interceptors.response.use(
  response => successHandler(response),
  error => errorHandler(error)
) 
```

现在 Axios 发出的每个请求都使用我们定义的处理程序。

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2019/global-http-request-and-response-handling-with-the-axios-interceptor)