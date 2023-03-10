# 拉勒维——拉勒维的漂亮仪表板

> 原文：<https://dev.to/tuandm/laravel--vuejs--laravue---a-beautiful-dashboard-for-laravel-3h11>

### 介绍

几个月前，我试图为我的项目找到一个新的解决方案，我用 Vue 建立了一个 SPA 仪表板(使用这个[伟大的框架](https://github.com/PanJiaChen/vue-element-admin)，Laravel [Lumen](https://lumen.laravel.com/) 作为 API 网关，Laravel [Passport](https://laravel.com/docs/5.7/passport) 作为 SSO 服务器)。经过几周的工作，我发现架构有一些局限性，特别是在部署和开源方面(因为涉及到很多组件，CORS 设置、...).后来有一天，我有了一个新想法:

> 我们为什么不用 Laravel 的 builtin VueJS 来搭建一个 SPA 仪表盘呢？

和幼虫+幼虫= [幼虫](https://github.com/tuandm/laravue)

演示: [https://laravue.dev](https://laravue.dev)
文档: [https://doc.laravue.dev](https://doc.laravue.dev)

[![Laravue](img/a7af5dcdd0414d2d714e0e1e9d91a6a6.png "Laravue")](https://res.cloudinary.com/practicaldev/image/fetch/s--tGD2efra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8xbnmrpaokp8n1vbk85.png)

Laravue 是一个漂亮的仪表盘，灵感来自于 [vue-element-admin](https://github.com/PanJiaChen/vue-element-admin) 但不仅限于此。它提供了为管理员构建企业应用程序以控制业务和管理他人所需的所有组件。我的计划是将 Laravel/Vue 的最新技术/库/组件应用到这个项目中，并让每个人都能轻松使用。在演示中，API 将由 Laravel 自己提供，其中大部分是伪造的，但易于实现。

### 入门

```
# Clone the project with composer
composer create-project tuandm/laravue
cd laravue

# Migration and DB seeder (after changing your DB settings in .env)
php artisan migrate --seed

# Install passport
php artisan passport:install

# install dependency
npm install

# Build for development
npm run dev # or npm run watch

# Start local development server
npm artisan serve 
```

### 下一步

*   这个项目正在进行大量的开发，它还没有被构建成 Laravel 插件(它应该是这样的)。下一步将是 Laravel 的一个独立插件，以方便整合到现有的 Laravel 网站。

*   提供完整的文档和严格的编码约定。PHP/Laravel 有优秀的[PSR](https://www.php-fig.org/psr/)vue js 有不错的标准[这里](https://vuejs.org/v2/style-guide/)但是太基础不够。

*   全面测试。

#### 我非常感谢任何反馈、意见、建议、...他们让我和这个图书馆变得更好。谢谢你。

此外，这是我的第一个开发职位🎉