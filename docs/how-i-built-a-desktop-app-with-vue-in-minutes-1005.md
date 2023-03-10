# 我如何在几分钟内用 vue 构建一个桌面应用

> 原文：<https://dev.to/thomas_ph35/how-i-built-a-desktop-app-with-vue-in-minutes-1005>

说到桌面 app，电子是一个强有力的工具。您可以一次性构建跨平台的应用程序。

因为我喜欢 vue，我已经用“电子 vue”创建了一个应用程序，这是多么简单！

让我们使用 OpenWeatherMap API 制作一个天气应用程序

# 🛠️安装

我用的是 Ubuntu 18.04 和 Webstorm IDE。我也喜欢 vuetify 组件，所以我安装了 vue tify/电子仓库

> 看来 GitHub 回购已经不存在了..

安装项目运行

```
vue init vuetifyjs/electron my-project

cd my-project
npm install
npm run dev 
```

### 现在你已经准备好了！

[![the boiler plate](img/da70cb1244a6c4d94e65dea639c8e923.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LekAm71b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://a.storyblok.com/f/50995/1438x845/577e2fb93b/capture-du-2018-12-04-14-45-32.png)

然后要显示天气，我需要:

-最高温度
-最低温度
-湿度

所以让我们把那一页改成我们需要的！我使用两个卡片组件，一个搜索城市，另一个显示天气

```
 <v-card>
        <v-card-text>
          <p>Welcome to my météo app.</p>
          <p>Search for a city to display the weather</p>
          <v-text-field label="City" box v-model="city"></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn primary flat router @click="getWeather">Search</v-btn>
        </v-card-actions>
      </v-card> 
```

```
 <v-card v-if="show">
        <v-card-text>
            <v-layout row>
                <v-layout xs6>
                    <v-card-text>
                        <v-spacer></v-spacer>
                        <h1>{{temp.toFixed(2)}}°C</h1>
                        <h1>{{weatherDescription}}</h1>
                    </v-card-text>
                </v-layout>
                <v-layout xs6>
                    <v-card-text>
                        <p><v-icon>fas fa-snowflake</v-icon>Min : {{ tempMin.toFixed(2) }}°C</p>
                        <p><v-icon>fas fa-sun</v-icon>Max : {{ tempMax.toFixed(2) }}°C</p>
                        <p><v-icon>fas fa-tint</v-icon>Humidity : {{ humidity }} %</p>
                    </v-card-text>
                </v-layout>
            </v-layout>
        </v-card-text>
      </v-card> 
```

# 💻请求 API

我现在需要编写 getWeather 函数

我使用 axios 发出 API 请求，然后将我想要的数据存储到我的应用程序中

> API 端点为'/数据/2.5/天气'

```
 import SystemInformation from './WelcomeView/SystemInformation'
  import axios from 'axios'
  axios.defaults.baseURL = 'http://api.openweathermap.org/data/2.5'
  export default {
    name: 'welcome',
    components: { SystemInformation },
    data () {
      return {
        city: '',
        country: '',
        weatherDescription: '',
        temp: null,
        tempMin: null,
        tempMax: null,
        humidity: null,
        show: false,
        key: '863668499362fb4884ebd97229f3b26b',
        url: 'http://api.openweathermap.org/data/2.5/weather'
      }
    },
    methods: {
      open (link) {
        this.$electron.shell.openExternal(link)
      },
      getWeather () {
        axios.get(this.url, {
          params: {
            q: this.city,
            appid: this.key
          }
        }).then(response => {
          this.temp = response.data.main.temp - 274
          this.tempMax = response.data.main.temp_max - 274
          this.tempMin = response.data.main.temp_min - 274
          this.humidity = response.data.main.humidity
          this.weatherDescription = response.data.weather[0].description
          this.show = true
        }).catch(errors => {
          console.log(errors)
        })
      }
    }
  } 
```

# 🌟就是这样！

简单如 classique Vue js 应用程序，我只是做了一个简单的跨平台应用程序。

[![my-final app in vue](img/8cf7bb3b60eb70b7dc66d3b396b86c3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zgk1dhCp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://a.storyblok.com/f/50995/1607x1031/2808431aba/capture-du-2018-12-04-15-07-12.png)

这是我的第一个电子应用，也是我的第一篇博文

我真的很喜欢它，因为我可以在 Windows、MacOs 和 ubuntu 上使用同一个应用程序(我用过 Ubuntu 吗)

请随意告诉我我做错了什么或者我可以做得更好，并分享一些需要改进的很酷的东西！