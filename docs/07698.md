# Axios 对现实应用的提示

> 原文：<https://dev.to/kevinleedrum/axios-tips-for-real-world-apps-3bo4>

这里是我收集的一些有用的模式和技巧，用于在你的 web 应用程序中用 axios 管理 API 请求。

## 模块

网上有很多 API 端点 URL 被硬编码到组件方法或生命周期挂钩中的例子。

这没有错，尤其是如果您只有几个 API 端点的话。然而，在大型应用程序中，创建一个专门用于处理我们的请求的模块可能会更好。

下面是一个硬编码 axios 请求的典型示例。

```
async function fetchData () {
  try {
    this.isLoading = true
    const response = await axios.get('/api/items') // <-------
    this.items = response.data
  } catch (error) {
    // handle error
  } finally {
    this.isLoading = false
  }
} 
```

让我们创建我们的 API 模块。

```
// api.js
import axios from 'axios'

export default {
  getItems: params => axios.get('/api/items', { params }),
  createItem: item => axios.post('/api/items', item),
  // etc.
} 
```

现在，我们可以轻松地在一个文件中查看和更新我们的所有端点，而我们应用程序的其余部分则不太关心我们如何或从哪里获取数据。

```
import api from './api'

// Inside your async method, component lifecycle hook, etc.
const response = await api.getItems()

// We can use the same method with our optional params argument
const response = await api.getItems({ id: 9 }) 
```

如果您的 API 有许多路径，那么将模块组织到名称空间中可能是有意义的。

```
// api.js

const comments = {
  get: params => axios.get('/api/v2/comments', { params }),
  delete: params => axios.delete('/api/v2/comments', { params }),
  // etc.
}

const posts = {
  get: params => axios.get('/api/v2/posts', { params }),
  // etc.
}

export default {
  comments,
  posts
} 
```

```
const responses = await Promise.all(
  api.posts.get({ id }),
  api.comments.get({ postId: id })
) 
```

如果您是一个 Vue 用户，并且您发现自己正在将 api 模块导入到许多组件中，那么可以考虑将它添加到 Vue 原型中。

```
// main.js
import api from '@/api'
import Vue from 'vue'

// ...

Vue.prototype.$api = api

const app = new Vue({
// ... 
```

现在，您的所有组件都将继承该属性作为`this.$api`。

```
// MyComponent.vue
methods: {
  async getCat (id) {
    try {
      this.isLoading = true
      const { data } = await this.$api.getCat(id)
    } catch (error) {
      // handle error
    } finally {
      this.isLoading = false
    }
  }
} 
```

## 基地路线

我们不想重复我们的基本路线和版本。为了避免这种情况，我们的第一个选择是简单地在模板字符串中添加一些变量。

```
const version = '2'
const API = `/api/${version}`

export default {
  getComments: params => axios.get(`${API}/comments`, params)
} 
```

一个更好的选择是创建一个 axios 的*实例*，专门针对我们的基本路线。

```
const API = axios.create({
  baseURL: '/api/v2'
})

export default {
  getComments: params => API.get('/comments', params)
} 
```

如果您的 API 版本是通过一个头而不是路由来完成的，那么创建一个 axios 实例会非常方便。只需给`axios.create`的配置对象添加一个`headers`属性。

```
const API = axios.create({
  baseURL: '/api',
  headers: { 'Accept-Version': '~2' }
}) 
```

## 您的应用

根据*您的*对*您的*应用程序的需求，混合搭配上述任何或所有提示。

也许你的 Vue 应用不需要 API 模块，但是你仍然想要一个针对你的基本路线的全局 axios 实例。

```
// main.js
import axios from 'axios'
import Vue from vue

const baseURL = '/api/v2'
Vue.prototype.$axios = axios.create({ baseURL })

// YourComponent.vue
this.$axios.post('/cats', data) // equivalent to axios.post('/api/v2/cats', data) 
```

请务必花些时间浏览一下 [axios 文档](https://github.com/axios/axios#example),因为其中有许多出色的特性。感谢阅读！