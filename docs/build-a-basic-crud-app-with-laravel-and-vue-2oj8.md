# 用 Laravel 和 Vue 构建一个基本的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/build-a-basic-crud-app-with-laravel-and-vue-2oj8>

Laravel 是当今最流行的 web 框架之一，因为它优雅、简单、可读性强。它还拥有最大、最活跃的开发者社区之一。Laravel 社区提供了大量有价值的教育资源，包括这个！在本教程中，您将构建一个琐事游戏作为两个独立的项目:一个 Laravel API 和一个 Vue 前端(使用 vue-cli)。这种方法提供了一些重要的好处:

*   它允许您分离后端和前端，并使用不同的测试和部署策略和时间表独立部署它们
*   您可以将您的前端作为静态应用程序部署到 CDN，并以托管后端的一小部分成本实现几乎无限的扩展
*   这种结构允许开发人员只在 API 或前端工作，而不需要访问系统其他部分的源代码(如果项目是集成的，这仍然有可能实现，但设置起来有点麻烦)，这使其成为大型团队的理想架构

在开始之前，您需要用 PHP 7 和 Node.js 8+/npm 设置一个开发环境。你还需要一个 [Okta 开发者账户](https://developer.okta.com/)，这样你就可以添加用户注册、用户登录以及所有其他与用户相关的功能。

有趣的事实:你知道 Vue 现在的流行很大程度上要归功于 Laravel 吗？Vue 预打包了 Laravel(以及 Laravel Mix，一个基于 webpack 的优秀构建工具),允许开发人员开始构建复杂的单页面应用程序，而无需担心 transpilers、代码打包、源代码映射或现代前端开发的任何其他“脏”方面。

## 创建 OpenID Connect 应用程序

在我们进入代码之前，让我们设置我们的 Okta 帐户，这样当我们需要它时它就准备好了。Okta 是一个 API 服务，允许您创建、编辑和安全地存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。你可以[在这里](https://developer.okta.com/signup/)注册一个永远免费的开发者账户。

注册后，登录并访问 Okta 仪表板。请务必注意仪表板右上角的 **Org URL** 设置，稍后在配置您的应用程序时，您将需要这个 URL。

接下来，设置一个新的应用程序，您将主要使用默认设置。以下是逐步说明:

进入**应用**菜单项，点击**添加应用**按钮:

[![Click Add Application](img/06d3475fa6d72dc5fd89723d9d4c2579.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UeQi0h7j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-laravel-vue-app/add-application-6c7f7a13ae7fdc6491fef895e8884fbe753933b59dbad9f7ef61d6ae31145ab0.png)

选择**单页应用**，点击**下一步**。

[![Create a new SPA application](img/cc3e327aa07aba21bf9d5f5f6e7c6f07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QHKqsFrS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-laravel-vue-app/new-spa-1a23df7a1064c4659963a8354bc65f2ebb4a5ad5cd54efdecab458406c89af13.png)

设置一个描述性的应用名称，添加`http://localhost:8080/implicit/callback`作为**登录重定向 URI** ，点击**完成**。您可以保留其余的设置。

最后，复制下**客户端 ID** 变量的值。这个值将在稍后的 OpenID 连接流中使用。

## 构建您的 Laravel 和 Vue CRUD 应用程序

现在是时候挖掘并构建一个有趣的琐事游戏应用程序了！这个应用程序将与一个免费的问答游戏问题的 API 集成，并允许我们建立一个玩家列表，加载问题，并标记玩家的答案是对还是错。

以下是您完成的应用程序的外观:

[![The completed application](img/95d1454623a698a16c258990cebd9d7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ojZWMcDv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-laravel-vue-app/finished-app-264ee496786d1314e942c4f29a003e072804c904ca1715f152694b15029326cd.png)

您可以创建自己的规则，但游戏的基本要点如下:

*   游戏主持人给玩家读问题并标记他们的答案
*   主持人不能是玩家
*   玩家可以尝试回答当前问题或通过
*   如果答案正确，玩家获得+1 分。如果答案是错误的，玩家得到-1 分。
*   当问题回答正确或所有人都通过后，主持人可以点击**刷新问题**按钮加载下一个问题。

## 安装 Laravel 并配置应用程序

一旦通过 composer 全局安装了`laravel`命令，您将使用它创建一个新的 Laravel 项目，并从它的目录:
启动开发 PHP 服务器

```
composer global require laravel/installer
laravel new trivia-web-service
cd trivia-web-service
php artisan serve 
```

接下来，您将为您的应用程序设置一个新的 MySQL 数据库和用户(MySQL 并不是一成不变的，如果您愿意，可以使用不同的数据库引擎):

```
mysql -uroot -p
CREATE DATABASE trivia CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'trivia'@'localhost' identified by 'trivia';
GRANT ALL on trivia.* to 'trivia'@'localhost';
quit 
```

现在您需要将数据库配置变量插入到。主项目目录中的 env 文件:

`.env`

```
DB_DATABASE=trivia
DB_USERNAME=trivia
DB_PASSWORD=trivia 
```

## 创建一个简单的 Laravel API

现在您的数据库已经配置好了，让我们来构建 API。您的 Laravel API 将非常简单，它将只包含一个实体(一个`Player`)。让我们为它创建一个迁移和数据库模型:

```
php artisan make:model Player -m
Model created successfully.
Created Migration: 2018_10_08_094351_create_players_table 
```

将创建数据库表的代码放入迁移的`up()`方法中:

`database/migrations/2018_10_08_094351_create_players_table.php`

```
public function up()
{
    Schema::create('players', function (Blueprint $table) {
        $table->increments('id');
        $table->string('name');
        $table->integer('answers')->default(0);
        $table->integer('points')->default(0);
        $table->timestamps();
    });
} 
```

接下来，运行迁移以将其应用到您的数据库:

```
php artisan migrate 
```

此时，您可能注意到您已经有了一个模型类，`app/Player.php`，但是它是空的。在创建或更新记录时，您需要告诉 Laravel 哪些字段可以批量分配。您将通过模型类的`$fillable`属性来实现这一点:

`app/Player.php`

```
class Player extends Model
{
    protected $fillable = ['name', 'answers', 'points'];
} 
```

Laravel 5.6 引入了 API 资源的概念，这大大简化了在 Laravel 中创建 REST APIs 的方式。API 资源类负责将我们的数据转换成 JSON 表示。这个 API 需要两个资源:一个`Player`(处理单个玩家)，一个`PlayerCollection`(处理一组玩家)。

```
php artisan make:resource Player
php artisan make:resource PlayerCollection 
```

转换在资源类的`toArray()`函数中定义:

`app/Http/Resources/Player.php`

```
public function toArray($request)
{
    return [
        'id' => $this->id,
        'name' => $this->name,
        'answers' => (int) $this->answers,
        'points' => (int) $this->points,
        'created_at' => $this->created_at,
        'updated_at' => $this->updated_at,
    ];
} 
```

`app/Http/Resources/PlayerCollection.php`

```
public function toArray($request)
{
    return [
        'data' => $this->collection
    ];
} 
```

这样一来，您现在可以为 REST API 创建路由和控制器了。

```
php artisan make:controller PlayerController 
```

`routes/api.php`

```
Route::get('/players', 'PlayerController@index');
Route::get('/players/{id}', 'PlayerController@show');
Route::post('/players', 'PlayerController@store');
Route::post('/players/{id}/answers', 'PlayerController@answer');
Route::delete('/players/{id}', 'PlayerController@delete');
Route::delete('/players/{id}/answers', 'PlayerController@resetAnswers'); 
```

`app/Http/Controllers/PlayerController.php`

```
...
use App\Player;
use App\Http\Resources\Player as PlayerResource;
use App\Http\Resources\PlayerCollection;
...

class PlayerController extends Controller
{
    public function index()
    {
        return new PlayerCollection(Player::all());
    }

    public function show($id)
    {
        return new PlayerResource(Player::findOrFail($id));
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|max:255',
        ]);

        $player = Player::create($request->all());

        return (new PlayerResource($player))
                ->response()
                ->setStatusCode(201);
    }

    public function answer($id, Request $request)
    {
        $request->merge(['correct' => (bool) json_decode($request->get('correct'))]);
        $request->validate([
            'correct' => 'required|boolean'
        ]);

        $player = Player::findOrFail($id);
        $player->answers++;
        $player->points = ($request->get('correct')
                           ? $player->points + 1
                           : $player->points - 1);
        $player->save();

        return new PlayerResource($player);
    }

    public function delete($id)
    {
        $player = Player::findOrFail($id);
        $player->delete();

        return response()->json(null, 204);
    }

    public function resetAnswers($id)
    {
        $player = Player::findOrFail($id);
        $player->answers = 0;
        $player->points = 0;

        return new PlayerResource($player);
    }
} 
```

您必须启用 CORS，这样您就可以从前端应用程序访问您的 API:

```
composer require barryvdh/laravel-cors 
```

`app/Http/Kernel.php`

```
protected $middlewareGroups = [
    'web' => [
        ...
        \Barryvdh\Cors\HandleCors::class,
    ],

    'api' => [
        ...
        \Barryvdh\Cors\HandleCors::class,
    ],
]; 
```

您的 API 允许您检索所有玩家或特定玩家，添加/删除玩家，将答案标记为正确/错误，并重置玩家的分数。还有一个请求验证，代码用少量代码生成带有适当 HTTP 状态代码的 JSON 响应。

要测试 API，只需向数据库中添加一些虚拟数据(使用 Faker 库来自动化这个过程)。之后，您可以访问这些 URL 并检查响应:

*   `http://127.0.0.1:8000/api/players`
*   `http://127.0.0.1:8000/api/players/1`

测试 POST/PUT/DELETE 请求有点复杂，需要一个外部工具(例如 cURL 或 [Postman](https://www.getpostman.com/) )。您还需要确保每个请求都发送了以下标头:

`Accept: "application/json"`

这个头告诉 Laravel 以 JSON 格式返回任何验证错误。

## 安装 Vue，设置前端应用

您将安装`vue-cli`并使用默认配置创建一个新的 Vue.js 项目。您还将向项目添加 Vue 路由器、Axios 和 Okta 认证+授权库:

```
npm install -g @vue/cli
vue create trivia-web-client-vue
cd trivia-web-client-vue
yarn add --save vue-router axios @okta/okta-vue
yarn serve 
```

加载`http://localhost:8080/`现在显示默认的 VueJS 应用。

## 在 Vue 前端创建一个带路由的菜单

先去掉默认内容，这样你会有一个漂亮的空白页:删除`src/components/HelloWorld.vue`和`src/App.vue`，修改`src/main.js`:

`main.js`

```
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.config.productionTip = false

Vue.use(VueRouter)

import Dashboard from './components/Dashboard.vue';

const routes = [
  { path: '/', component: Dashboard },
]

const router = new VueRouter({
  mode: 'history',
  routes
})

new Vue({
  router,
  render: h => h(Dashboard)
}).$mount('#app') 
```

创建一个新文件`components/Dashboard.vue`:

`components/Dashboard.vue`

```
<template>
    <h1>This is the dashboard</h1>
</template>

<script>
</script> 
```

用默认的浏览器字体看起来不太好看。让我们通过从 CDN 加载布尔玛 CSS 框架来改进它:

`public/index.html`

```
...
<link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.1/css/bulma.min.css"> 
```

## 给 Vue 前端添加认证

太好了！现在，您可以添加您的菜单和路线，并实现一个需要验证的受保护的“琐事游戏”路线:

`main.js`

```
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.config.productionTip = false
Vue.use(VueRouter)

import Dashboard from './components/Dashboard.vue'
import Auth from '@okta/okta-vue'

Vue.use(Auth, {
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  client_id: '{yourClientId}',
  redirect_uri: 'http://localhost:8080/implicit/callback',
  scope: 'openid profile email'
})

const routes = [
  { path: '/implicit/callback', component: Auth.handleCallback() },
  { path: '/', component: Dashboard},
]

const router = new VueRouter({
  mode: 'history',
  routes
})

new Vue({
  router,
  render: h => h(Dashboard)
}).$mount('#app') 
```

别忘了替换你自己的 Okta 域名和客户端 ID！你还需要添加一个菜单与一个“琐事游戏”链接(只有在认证)和登录/注销按钮到仪表板。

`main.js`

```
...
import TriviaGame from './components/TriviaGame.vue'

const routes = [
  { path: '/implicit/callback', component: Auth.handleCallback() },
  { path: '/trivia', component: TriviaGame }
]
... 
```

`components/TriviaGame.vue`

```
<template>
    <h1>This is the trivia game</h1>
</template>

<script>
</script> 
```

`components/Dashboard.vue`

```
<template>
    <div style="text-align:center">
        <section class="section">
            <div class="container">
                <nav class="navbar" role="navigation" aria-label="main navigation">
                    <div class="navbar-menu">
                        <div class="navbar-item">
                            <router-link to="/" class="navbar-item">Home</router-link>
                            <router-link v-if='authenticated' to="/trivia" class="navbar-item">Trivia Game</router-link>
                            <a class="button is-light" v-if='authenticated' v-on:click='logout' id='logout-button'> Logout </a>
                            <a class="button is-light" v-else v-on:click='login' id='login-button'> Login </a>
                        </div>
                    </div>
                </nav>
                <router-view></router-view>
            </div>
        </section>
    </div>
</template>

<script>
export default {

    data: function () {
        return {
            authenticated: false
        }
    },

    created () {
        this.isAuthenticated()
    },

    watch: {
        // Everytime the route changes, check for auth status
        '$route': 'isAuthenticated'
    },

    methods: {
        async isAuthenticated () {
            this.authenticated = await this.$auth.isAuthenticated()
        },

        login () {
            this.$auth.loginRedirect('/')
        },

        async logout () {
            await this.$auth.logout()
            await this.isAuthenticated()

            // Navigate back to home
            this.$router.push({ path: '/' })
        }
    }
}
</script> 
```

该应用程序现在包含一个导航栏，带有主页、琐事游戏(仅在登录时可用)和登录或注销按钮(取决于登录状态)的占位符页面。登录/注销操作通过 Okta 完成。您现在可以继续执行琐事游戏并连接后端 API。

### 从 Laravel API 获取玩家名单

接下来，您将添加一个新的 Vue 组件来从 Laravel API 加载球员列表。

您将创建一个新的`src/config.js`文件，并在那里定义我们的基本 API url:

`src/config.js`

```
export const API_BASE_URL = 'http://localhost:8000/api'; 
```

您现在可以修改您的`TriviaGame.vue`组件了:

`components/TriviaGame.vue`

```
<template>
    <div>
        <div v-if="isLoading">Loading players...</div>
        <div v-else>
        <table class="table">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Answers</th>
                    <th>Points</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                <template v-for="player in players">
                    <tr v-bind:key="player.id">
                        <td>{{ player.id }}</td>
                        <td>{{ player.name }}</td>
                        <td>{{ player.answers }}</td>
                        <td>{{ player.points }}</td>
                        <td>Action buttons</td>
                    </tr>
                </template>
            </tbody>
        </table>
        <a class="button is-primary">Add Player</a>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { API_BASE_URL } from '../config'

export default {
    data() {
        return {
            isLoading: true,
            players: {}
        }
    },
    async created () {
        axios.defaults.headers.common['Authorization'] = `Bearer ${await this.$auth.getAccessToken()}`
        try {
            const response = await axios.get(API_BASE_URL + '/players')
            this.players = response.data.data
            this.isLoading = false
        } catch (e) {
            // handle the authentication error here
        }
    }
}
</script> 
```

## 向 Laravel API 添加认证

您需要保护您的后端 API，以便它只允许包含有效 Okta 令牌的请求。您将安装 Okta JWT 验证器包，并添加一个用于 API 认证的定制中间件:

```
composer require okta/jwt-verifier spomky-labs/jose guzzlehttp/psr7
php artisan make:middleware AuthenticateWithOkta 
```

`app/Http/Middleware/AuthenticateWithOkta.php`

```
<?php
namespace App\Http\Middleware;

use Closure;

class AuthenticateWithOkta
{
    /**
     * Handle an incoming request.
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($this->isAuthorized($request)) {
            return $next($request);
        } else {
            return response('Unauthorized.', 401);
        }
    }

    public function isAuthorized($request)
    {
        if (! $request->header('Authorization')) {
            return false;
        }

        $authType = null;
        $authData = null;

        // Extract the auth type and the data from the Authorization header.
        @list($authType, $authData) = explode(" ", $request->header('Authorization'), 2);

        // If the Authorization Header is not a bearer type, return a 401.
        if ($authType != 'Bearer') {
            return false;
        }

        // Attempt authorization with the provided token
        try {

            // Setup the JWT Verifier
            $jwtVerifier = (new \Okta\JwtVerifier\JwtVerifierBuilder())
                            ->setAdaptor(new \Okta\JwtVerifier\Adaptors\SpomkyLabsJose())
                            ->setAudience('api://default')
                            ->setClientId('{yourClientId}')
                            ->setIssuer('{yourIssuerUrl}')
                            ->build();

            // Verify the JWT from the Authorization Header.
            $jwt = $jwtVerifier->verify($authData);
        } catch (\Exception $e) {

            // You encountered an error, return a 401.
            return false;
        }

        return true;
    }

} 
```

当然，您需要将客户端 ID 和发行者 URL 替换为您自己的 ID 和 URL！最好将这些变量提取到`.env`文件中。它们不是秘密，它们在前端应用程序中是可见的，所以将它们保存在 repo 中没有安全问题，但是如果您有多个环境，这就不方便了。

`app/Http/Kernel.php`

```
 protected $middlewareGroups = [
        'web' => [
            ...
        ],

        'api' => [
            ...
            \App\Http\Middleware\AuthenticateWithOkta::class,
        ],
    ]; 
```

如果你做的一切都是正确的，`http://localhost:8000/api/players`现在应该显示一个“未授权”消息，但在 Vue 前端加载球员名单应该工作正常(当你登录时)。

## 在 Vue 中创建新的播放器组件

接下来，用一个添加新玩家的表单替换“添加玩家”按钮占位符。

`components/TriviaGame.vue`

```
Replace 
<a class="button is-primary">Add Player</a>
with:
<player-form @completed="addPlayer"></player-form>

Add to the <script> section:

import PlayerForm from './PlayerForm.vue'

export default {
    components: {
        PlayerForm
    },
...
    methods: {
        addPlayer(player) {
            this.players.push(player)
        }
    } 
```

创建一个新组件`PlayerForm.vue`:

`components/PlayerForm.vue`

```
<template>
    <form @submit.prevent="onSubmit">
        <span class="help is-danger" v-text="errors"></span>

        <div class="field">
            <div class="control">
                <input class="input" type="name" placeholder="enter player name..." v-model="name" @keydown="errors = ''">
            </div>
        </div>

        <button class="button is-primary" v-bind:class="{ 'is-loading' : isLoading }">Add Player</button>
    </form>
</template>

<script>
import axios from 'axios'
import { API_BASE_URL } from '../config'

export default {
    data() {
        return {
            name: '',
            errors: '',
            isLoading: false
        }
    },
    methods: {
        onSubmit() {
            this.isLoading = true
            this.postPlayer()
        },
        async postPlayer() {
            axios.defaults.headers.common['Authorization'] = `Bearer ${await this.$auth.getAccessToken()}`
            axios.post(API_BASE_URL + '/players', this.$data)
                .then(response => {
                    this.name = ''
                    this.isLoading = false
                    this.$emit('completed', response.data.data)
                })
                .catch(error => {
                    // handle authentication and validation errors here
                    this.errors = error.response.data.errors
                    this.isLoading = false
                })
        }
    }
}
</script> 
```

现在可以给我们的游戏添加更多的玩家了。

## 在 Vue 应用程序中添加一个“删除球员”按钮

接下来，您将使用一个实际删除播放器的按钮来替换“动作按钮”占位符。

`components/TriviaGame.vue`

```
Replace
<td>Action buttons</td>
with:
<td>
<button class="button is-primary" v-bind:class="{ 'is-loading' : isDeleting(player.id) }" @click="deletePlayer(player.id)">Delete Player</button>
</td>

Modify the <script> section:

...
import Vue from 'vue'
...

export default {
    ...
    methods: {
        ...
        isDeleting(id) {
            let index = this.players.findIndex(player => player.id === id)
            return this.players[index].isDeleting
        },
        async deletePlayer(id) {
            let index = this.players.findIndex(player => player.id === id)
            Vue.set(this.players[index], 'isDeleting', true)
            await axios.delete(API_BASE_URL + '/players/' + id)
            this.players.splice(index, 1)
        }
    }
} 
```

## 连接一个琐事问题服务到 Vue

为了节省时间，您将使用一个公共 API 来检索琐事问题，而不是从头构建一个问题数据库。该服务提供了一个 URL，每次请求时都会返回一个不同的小问题。在`config.js`文件中定义 URL，当加载琐事游戏页面时，您会得到一个初始问题。然后，您将修改 Trivia Game 组件，以包含一张带有问题和答案的卡片:

`src/config.js`

```
...
export const TRIVIA_ENDPOINT = 'http://jservice.io/api/random?count=1'; 
```

`components/TriviaGame.vue`(粘贴文件的全部内容)

```
<template>
    <div class="columns">
        <div class="column" v-if="isLoading">Loading players...</div>
        <div class="column" v-else>
        <table class="table">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Answers</th>
                    <th>Points</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                <template v-for="player in players">
                    <tr v-bind:key="player.id">
                        <td></td>
                        <td></td>
                        <td></td>
                        <td></td>
                        <td>
                        <button class="button is-primary" v-bind:class="{ 'is-loading' : isDeleting(player.id) }" @click="deletePlayer(player.id)">Delete Player</button>
                        </td>
                    </tr>
                </template>
            </tbody>
        </table>
        <player-form @completed="addPlayer"></player-form>
        </div>
        <div class="column">
            <div class="card" v-if="question">
                <div class="card-content">
                    <button class="button is-primary" @click="getQuestion()">Refresh Question</button>
                    <p class="title">

                    </p>
                    <p class="subtitle">

                    </p>
                </div>
                <footer class="card-footer">
                    <p class="card-footer-item">
                        <span>Correct answer: </span>
                    </p>
                </footer>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { API_BASE_URL, TRIVIA_ENDPOINT } from '../config'
import PlayerForm from './PlayerForm.vue'
import Vue from 'vue'

export default {
    components: {
        PlayerForm
    },
    data() {
        return {
            isLoading: true,
            question: null,
            players: {}
        }
    },
    async created () {
        this.getQuestion()
        axios.defaults.headers.common['Authorization'] = `Bearer ${await this.$auth.getAccessToken()}`
        try {
            const response = await axios.get(API_BASE_URL + '/players')
            this.players = response.data.data
            this.isLoading = false
        } catch (e) {
            // handle the authentication error here
        }
    },
    methods: {
        async getQuestion() {
            delete axios.defaults.headers.common.Authorization
            this.doGetQuestion()
            axios.defaults.headers.common['Authorization'] = `Bearer ${await this.$auth.getAccessToken()}`
        },
        async doGetQuestion() {
            try {
                const response = await axios.get(TRIVIA_ENDPOINT, {
                    headers: {
                        'Content-Type': 'application/x-www-form-urlencoded' 
                    }
                })
                this.question = response.data[0]
            } catch (e) {
                // handle errors here
            }
        },
        addPlayer(player) {
            this.players.push(player)
        },
        isDeleting(id) {
            let index = this.players.findIndex(player => player.id === id)
            return this.players[index].isDeleting
        },
        async deletePlayer(id) {
            let index = this.players.findIndex(player => player.id === id)
            Vue.set(this.players[index], 'isDeleting', true)
            await axios.delete(API_BASE_URL + '/players/' + id)
            this.players.splice(index, 1)
        }
    }
}
</script> 
```

### 在 Vue 中添加按钮表示正确和错误答案

现在，让我们在**删除玩家**按钮旁边再添加两个按钮，并实现处理程序:

`components/TriviaGame.vue`

```
...
<td>
    <button class="button is-primary" v-bind:class="{ 'is-loading' : isCountUpdating(player.id) }" @click="answer(player.id, true)">(+1) Right</button> 
    <button class="button is-primary" v-bind:class="{ 'is-loading' : isCountUpdating(player.id) }" @click="answer(player.id, false)">(-1) Wrong</button> 
    <button class="button is-primary" v-bind:class="{ 'is-loading' : isDeleting(player.id) }" @click="deletePlayer(player.id)">Delete Player</button>
</td>
...

    methods: {
    ...
        isCountUpdating(id) {
            let index = this.players.findIndex(player => player.id === id)
            return this.players[index].isCountUpdating
        },
        async answer(id, isCorrectAnswer) {
            let data = {
                correct: isCorrectAnswer
            }
            let index = this.players.findIndex(player => player.id === id)
            Vue.set(this.players[index], 'isCountUpdating', true)
            const response = await axios.post(API_BASE_URL + '/players/' + id + '/answers', data)
            this.players[index].answers = response.data.data.answers
            this.players[index].points = response.data.data.points
            this.players[index].isCountUpdating = false
        }
    } 
```

游戏现在完成了！现在，您有了一个基本的 Laravel API，它将琐事问题返回给经过身份验证的请求，还有一个 Vue 前端，它可以让用户登录并向 Laravel API 发出经过身份验证的请求。

这是一个很好的开端，但当然还有改进的余地。您可以通过将通用 API 样板代码(检索访问令牌、发送授权头、发送请求和接收响应)提取到服务类中来改进代码。

你可以在这里找到完整的代码:[https://github . com/okta developer/okta-PHP-laravel-vue-crud-example](https://github.com/oktadeveloper/okta-php-laravel-vue-crud-example)

## 了解更多关于 Laravel、Vue 和 Okta 的信息

如果您想更深入地了解本文涉及的主题，以下资源是一个很好的起点:

*   [我们的 Vue/PHP 快速入门指南](https://developer.okta.com/quickstart/#/vue/php/generic)
*   [用 Laravel 和 Angular 构建一个基本的 CRUD 应用](https://dev.to/oktadev/tutorial-build-a-basic-crud-app-with-laravel-and-angular-2g2m-temp-slug-1893918)
*   [用 Laravel 和 React 构建一个基本的 CRUD 应用](https://dev.to/oktadev/build-a-basic-crud-app-with-laravel-and-react-6ok-temp-slug-547211)

如果您发现任何问题，请在下面添加评论，我们将尽力帮助您。如果你喜欢这个教程，你应该在 Twitter 上关注我们。我们还有一个 [YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)，在那里我们发布截屏和其他视频。