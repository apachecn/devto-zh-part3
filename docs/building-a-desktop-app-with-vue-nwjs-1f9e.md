# 用 Vue: NW.js 构建桌面应用

> 原文：<https://dev.to/n_tepluhina/building-a-desktop-app-with-vue-nwjs-1f9e>

之前我写过两篇关于用 Vue 框架构建桌面应用的文章:一篇是关于 Vuido 库的，另一篇是关于 T2 电子的。我收到一些评论，要求写一篇关于 [NW.js](https://nwjs.io/) 的文章，我答应写一篇...开始了。迟做比不做好😅

> NW.js(以前称为 node-webkit)是一个用 HTML、CSS 和 JavaScript 构建桌面应用程序的框架。它基于[铬](http://www.chromium.org/)和 [Node.js](https://nodejs.org/en/) 。NW.js 允许您直接从浏览器调用 Node.js 代码和模块，也可以在您的应用程序中使用 Web 技术。此外，您可以轻松地将 web 应用程序打包到本机应用程序中。

## ⚛️与电子的差异

Electron 和 NW.js 有许多共同的特性:它们都是基于 Chromium 和 Node.js 构建的，并且使用 HTML、CSS 和 js 来构建桌面应用。但是它们也有一些显著的区别:

*   在 NW.js 中，应用程序的主要入口点是网页或 js 脚本。您通过`package.json` `main`属性指定该条目。在 electronic 中，入口点是一个 JavaScript 脚本。您可以手动创建一个浏览器窗口，并使用 API 加载一个 HTML 文件，而不是直接提供 URL。
*   在构建过程中，NW.js 构建所有的 ChromiumElectron 使用 [libchromiumcontent](https://github.com/electron/libchromiumcontent) 来访问 Chromium 的内容 API
*   NW.js 有[内置支持](http://docs.nwjs.io/en/latest/For%20Users/Advanced/Protect%20JavaScript%20Source%20Code/)将你的源代码编译成受保护的二进制文件。electronic 将其应用程序与 [asar](https://github.com/electron/asar) 打包在一起，其中包含应用程序未受保护的源代码。

## 💻我们要建造什么

现在，当我们理解了与 electronic 的区别后，我们将构建与上一篇文章中相同的应用程序:一个基于 [OpenWeatherMap API](https://openweathermap.org/api) 的应用程序，用于查看用户选择的城市的天气。这个项目将使用 [Vue CLI](https://cli.vuejs.org/) 构建，我推荐这种方式来搭建任何 Vue 应用程序。

如果你只想检查最终的 NW.js app 代码，这里是[这里是](https://gitlab.com/ntepluhina/Vue-Desktop-App/tree/nw-js)。

## 🛠️安装

### 使用 Vue CLI 创建应用程序

首先，让我们用 Vue CLI 搭建一个应用程序。请确保您已经安装了它，在您的控制台中键入以下命令:

```
vue --version 
```

如果你没有版本或者版本低于 3.x，请运行

```
npm install -g @vue/cli 
```

现在您可以从控制台创建一个 Vue 项目了！为此，请运行

```
vue create <<YOUR-PROJECT-NAME>> 
```

并选择您需要的选项。我将使用这个项目的默认设置。

太好了，现在我们准备添加一些桌面魔术🧙

### 添加 NW.js

[NW.js 官方文档](http://docs.nwjs.io/en/latest/For%20Users/Package%20and%20Distribute/)推荐使用两个构建器之一来打包您的应用程序:

*   [nwjs-builder-phoenix](https://github.com/evshiron/nwjs-builder-phoenix)
*   [nw-builder](https://github.com/nwjs-community/nw-builder)

我选择了 nwjs-builder-phoenix，假设您也将使用它，我将描述进一步的步骤😅

所以，首先你需要把它作为一个依赖项添加到你的项目中:

```
npm install --save-dev nwjs-builder-phoenix
## OR
yarn add --dev nwjs-builder-phoenix 
```

现在您需要修改您的`package.json`文件，为打包程序提供一个正确的条目。

> 提示:您可以在应用程序的根文件夹中找到`package.json`。

应该在`main`属性下指定打包程序条目。我们将在桌面上运行一个开发环境，所以让我们用下面的
来扩展我们的`package.json`

```
"main":  "http://localhost:8080" 
```

这是我们搭建的 web 应用程序在本地运行的端口。通常`main`是`index.html`,但是现在我们想在每次应用程序改变时进行热重装。这就是为什么我们把`main`指向一个带有热重装模块的 Vue 应用。

### 运行应用程序

现在让我们运行我们的 web 应用程序:

```
npm run serve
## OR
yarn serve 
```

您可以在浏览器中检查`http://localhost:8080`，以确保某个应用程序正在运行。应该是这样的:

[![Application in browser](img/1c13c90e172a9905234a383a3e452fbc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3d-FXlm1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vx8vale.png)

最后，我们准备启动一个桌面应用:

```
./node_modules/.bin/run . 
```

> 这将获取 NW.js 的一个指定版本(默认为`sdk`)并运行一个开发环境。
> 
> `run`是由`nwjs-builder-phoenix`添加的命令，点表示我们正在使用根目录下的`package.json`。

现在你应该有一个应用程序运行在桌面上

[![Application on desktop](img/11991840738e9a088eeaee68a1e51dca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--46rZDfcP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mbF91p1.png)

让我们稍微自动化运行开发环境。关闭你的桌面应用并安装 NW.js SDK 版本:

```
npm install --save-dev nw@sdk
## OR
yarn add --dev nw@sdk 
```

现在，向`package.json` `scripts`部分添加一个新任务:

```
"scripts": {
  "serve": "vue-cli-service serve",
  "build": "vue-cli-service build",
  "nw-serve": "nw ." /* <--- here is your new npm task */
}, 
```

让我们试着运行一下:

```
npm run nw-serve
## OR
yarn nw-serve 
```

你应该有你的应用程序再次运行在桌面上，但现在没有下载步骤🎉

好消息:您可以像在浏览器中一样简单地打开 devtools，并使用它们来调试您的应用程序！只需点击`Window`->-`Devtools`

[![Dev Tools](img/944fe76ee3eaeffb2acaf2b1b4734d9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cuiO5jq4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hv14cFD.png)

## 🕹️搭起一个 app

与 Electron 类似，NW.js 驱动的应用程序的构建类似于普通的 web 应用程序，因此我们将创建一个 web 应用程序，用一些 CSS 对其进行样式化，并让`nwjs-builder-phoenix`负责将其打包成桌面应用程序。

> 注意:和电子 app 一样，我没有故意安装任何 CSS 框架或组件库:我想在不添加任何不同依赖项的情况下比较包大小。唯一用于所有桌面项目的库是 [axios](https://github.com/axios/axios) 。

打开`App.vue`文件，用以下代码替换其内容:

```
<template>
  <div id="app">
    <p>Enter the city name to check current weather in it</p>
    <section class="weather-input">
      <input type="text" v-model="query">
      <button :disabled="!query.length">Check</button>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: "",
    };
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.button {
  color: #000;
}
</style> 
```

现在我们的应用程序看起来是这样的:

[![Unstyled app](img/e4df59808099df2c439706d4f8d6d8bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--moxfB1hu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/tECSCG3.png)

## 🔗进行 API 调用

我使用了 [OpenWeatherMap 当前天气 API](https://openweathermap.org/current) 。它给了你很多不同的信息，你可以在这里查看 JSON 响应的例子。

我们将使用 [axios](https://github.com/axios/axios) 来进行 API 调用。显然，我们需要安装它:

```
npm install axios
## OR
yarn add axios 
```

为了能够在任何 Vue 组件中使用 axios，我们将把它导入到`main.js`，设置基本 URL，然后在 Vue prototype 中为它创建一个属性:

```
//main.js

import axios from 'axios'

axios.defaults.baseURL = 'http://api.openweathermap.org/data/2.5';
Vue.http = Vue.prototype.$http = axios; 
```

现在，在`App.vue`中，我们将创建一组数据属性来显示不同的天气数据:

```
// App.vue

data() {
    return {
      query: '',
      error: false,
      city: '',
      country: '',
      weatherDescription: '',
      temp: null,
      tempMin: null,
      tempMax: null,
      humidity: null,
      icon: '',
    };
}, 
```

类似于电子，我增加了一个额外的属性，这是一个`icon`。API 提供了一个天气图标，但我们不能在 Vuido 应用程序中使用它，因为目前不支持显示图像。

让我们也创建一个获取数据的方法:

```
methods: {
  showWeather() {
    this.$http
      .get(`/weather?q=${this.query}&units=metric&&appid=${API_KEY}`)
      .then(response => {
        this.city = response.data.name;
        this.country = response.data.sys.country;
        this.weatherDescription = response.data.weather[0].description;
        this.temp = response.data.main.temp;
        this.tempMin = response.data.main.temp_min;
        this.tempMax = response.data.main.temp_max;
        this.humidity = response.data.main.humidity;
        this.icon = `http://openweathermap.org/img/w/${
          response.data.weather[0].icon
        }.png`;
        this.error = false;
      })
      .catch(() => {
        this.error = true;
        this.city = '';
      });
  },
}, 
```

> 不要忘记用你的 OpenWeather API 密匙创建一个 const `API_KEY`！

并添加到我们按钮的点击回调:

```
<button :disabled="!query.length" @click="showWeather">Check</button> 
```

现在，如果您在输入字段中输入文本并点击按钮，您可以在`Network`选项卡中观察到 API 调用:

[![API call response](img/ebe56ff30ae44dd1f0a09b170b3ba024.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qCJELojf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1YyIUHN.png)

## 💅显示天气数据

让我们将这些数据添加到模板:

```
<template>
  <main id="app">
    <p>Enter the city name to check current weather in it</p>
    <section class="weather-input">
      <input type="text" v-model="query">
      <button :disabled="!query.length" @click="showWeather">Check</button>
    </section>
    <section v-if="error" class="weather-error">
      There is no such city in the database
    </section>
    <section v-if="city.length" class="weather-result">
      <h1>{{city}}, {{country}}</h1>
      <p><em>{{weatherDescription}}</em></p>
      <div class="weather-result__main">
        <img :src="icon" alt="Weather icon">
        <div class="weather-result__temp">
          {{temp}}&deg;C
        </div>
      </div>
      <div class="weather-result__details">
        <p>Min: {{tempMin}}&deg;C</p>
        <p>Max: {{tempMax}}&deg;C</p>
        <p>Humidity: {{humidity}}%</p>
      </div>
    </section>
  </main>
</template> 
```

我们的应用视图:

[![App without styling](img/c94b0ecd79d24305176bd8a732070289.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X72VGrGn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Qic11v4.png)

看起来还是太基础了，是时候加点 CSS 了。让我们用下面的代码替换掉`App.vue`的整个`style`部分:

```
<style>
* {
  margin: 0;
  padding: 0;
}
html,
body,
#app {
  height: 100%;
}

#app {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 16px;
  padding: 10px;
  background: rgb(212, 228, 239);
  background: radial-gradient(
    ellipse at center,
    rgba(212, 228, 239, 1) 0%,
    rgba(134, 174, 204, 1) 100%
  );
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#d4e4ef', endColorstr='#86aecc',GradientType=1 ); /* IE6-9 fallback on horizontal gradient */
}

.weather-input {
  display: flex;
  align-items: center;
  padding: 20px 0;
}

.weather-result {
  text-align: center;
}

.weather-result__main {
  display: flex;
  align-items: center;
  justify-content: center;
  padding-top: 5px;
  font-size: 1.3rem;
  font-weight: bold;
}

.weather-result__details {
  display: flex;
  align-items: center;
  justify-content: space-around;
  color: dimgray;
}

.weather-error {
  color: red;
  font-weight: bold;
}

input {
  width: 75%;
  outline: none;
  height: 20px;
  font-size: 0.8rem;
}

button {
  display: block;
  width: 25%;
  height: 25px;
  outline: none;
  border-radius: 5px;
  white-space: nowrap;
  margin: 0 10px;
  font-size: 0.8rem;
}
</style> 
```

最后，我们有一个漂亮的全功能应用程序:

[![App ready](img/28ef360dbb3466f3cbb1447112e49ed1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZeDi03Yk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/5kP9seC.png)

打包之前要做的最后一件事是缩小窗口大小。为此，我们需要向`package.json` :
添加一个`window`属性

```
"window": {
  "width": 600,
  "height": 400
}, 
```

## 📦包装

要用`nwjs-builder-phoenix`打包一个 NW.js 应用程序，你需要用一组合适的参数运行一个构建命令。我将在 Mac 上构建它，以比较它与 Electron 和 Vuido 应用程序的大小。

首先，我们需要指定在构建过程中使用的 NW.js 版本。我们可以通过`package.json`
的`build`属性来实现

```
"build":  {  "nwVersion":  "0.35.3"  }, 
```

然后让我们在终端中运行一个构建命令:

```
./node_modules/.bin/build --tasks mac-x64 . 
```

> 同样，命令中的点意味着我们正在使用位于根项目文件夹中的`package.json`文件

让我们检查一下包装尺寸...233 兆字节😱！

[![App size](img/a687affa66c61e5e30c26f1898fd93cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WlUQjiv3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/UyyEIVT.png)

哇，太多了。甚至比电子申请还大！

## 🌟结论

**优点:**

*   非常容易开始
*   可以通过 CSS 样式定制
*   好医生
*   支持受保护的二进制文件

**缺点**

*   超大包装尺寸
*   不太好的命名(可能只有我，但电子/Vuido 比 NW.js 更令人难忘)
*   小生态系统。

## 💖特别感谢

我要感谢 Jared Wilcurt，他帮我解答了 NW.js 的问题。贾里德是 [NW.js Vue devtools](https://github.com/TheJaredWilcurt/nw-vue-devtools) 的作者，负责在桌面上调试 Vue 应用程序。

**更新**:如果你想知道如何显著减小包装尺寸，[这里有一篇 Jared Wilcurt 的文章](https://dev.to/thejaredwilcurt/reducing-app-distribution-size-in-nwjs-3d5f)。