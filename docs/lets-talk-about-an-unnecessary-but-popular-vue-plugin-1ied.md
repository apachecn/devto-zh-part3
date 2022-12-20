# 先说一个没必要但很流行的 Vue 插件

> 原文：<https://dev.to/heftyhead/lets-talk-about-an-unnecessary-but-popular-vue-plugin-1ied>

几天前，一些关于包含恶意代码的流行 npm 包的消息像病毒一样传播开来。整个事件提醒我们，在向我们的依赖项添加另一个包之前，我们应该三思。

这也让我想起了一个我见过几次弹出的不必要的 Vue 插件。Vue 温和的学习曲线使它成为初学者的热门选择，对他们来说，弄清楚自己写什么和安装什么就更难了。

# 违法者

我要说的包/插件是 vue-axios。如果你谷歌“vue axios”这是第一个结果。我认为这是它受欢迎的主要原因。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【imcvample】](https://github.com/imcvampire)/[视图-公理](https://github.com/imcvampire/vue-axios)

### 一个集成 axios 和 Vuejs 的小包装器

<article class="markdown-body entry-content container-lg" itemprop="text">

# vue-axios

一个集成 axios 和 Vuejs 的小包装器

## 如何安装:

### CommonJS:

```
npm install --save axios vue-axios
```

在您的条目文件中:

```
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios, axios)
```

### 脚本:

只需在你的`document`中依次添加 3 个脚本:`vue`、`axios`、`vue-axios`。

## 用法:

如果使用单个文件组件，这个包装器将`axios`绑定到`Vue`或`this`。

你可以这样使用`axios`:

```
Vue.axios.get(api).then((response) => {
  console.log(response.data)
})
this.axios.get(api).then((response) => {
  console.log(response.data)
})
this.$http.get(api).then((response) => {
  console.
```

…</article>

[View on GitHub](https://github.com/imcvampire/vue-axios)

我们来看看一个 Github 星数 1000+周下载量 23000 的插件是干什么的。我们可以从阅读一段描述开始:

> ### 用法:
> 
> 这个包装器将 axios 绑定到 Vue，或者如果你使用单个文件组件的话。

还有一个代码示例让插件的使用更加清晰:

```
Vue.axios.get(api).then((response) => {
  console.log(response.data)
})

this.axios.get(api).then((response) => {
  console.log(response.data)
})

this.$http.get(api).then((response) => {
  console.log(response.data)
}) 
```

基本上，这个包允许你一次导入 axios，然后在每个组件中使用它。

其实挺有用的。您不仅不必在每个组件中导入 axios，还可以使用自定义配置创建 axios 实例，并在所有组件中使用它。然而，在插件的描述中并没有提到，所以我不确定安装插件的人是否意识到了这一点。

# 一种另类

我们认为这个插件非常有用。那么问题是什么呢？让我们在不使用插件的情况下编写相同的功能:

```
import Vue from 'vue'
import axios from "axios";

Vue.prototype.$http = axios;
Vue.prototype.axios = axios; 
```

我们来对比一下配置插件所需的代码:

```
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios) 
```

正如我们所看到的，我们自己编写整个功能所需的行数与配置插件所需的行数相同。

最后，让我们展示一下这种使用 axios 和 Vue 的方法的稍微加强版:

```
import Vue from 'vue'
import axios from "axios";

const instance = axios.create({
  baseURL: 'https://myapi.com'
});

const instanceUserApi = axios.create({
  baseURL: 'https://userapi.com'
});
instanceUserApi.defaults.headers.common["Authorization"] =
  "Token" + localStorage.getItem("authToken");

Vue.prototype.$http = instance;
Vue.prototype.$httpUserApi = instanceUserApi; 
```

我们可以创建几个 axios 实例，每个实例都有不同的配置。这个插件不仅没有提供任何价值，而且还不如我们的代码灵活。只是为了说明这一点，插件允许你在配置过程中通过传递一个对象来创建许多 axios 实例。

# 区别与借口

如本期 Github 所述:【Vue.prototype 和 vue-axios 的区别？#18

该插件使得属性(axios 和$http)不可变。这对于某些人来说可能是优于上一段中描述的方法的优点。然而，我很有信心，使用这个插件的绝大多数开发者并不真正关心不变性。

# 结论

Vue-axios 插件正如它的描述所说。在我看来，这里没有不诚实或任何恶意。只是一些不知情的开发人员，他们不会再三考虑在他们的项目中添加什么。

如何看待这样的小插件/包？你认为这种插件创建者应该公开替代方案吗？