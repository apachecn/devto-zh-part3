# [随机]循环类递归

> 原文：<https://dev.to/junior/random-circular-class-recurrence-1jmn>

*没有实际内容只是代号*

```
// file: ./src/classes/User/index.js
const { r } = require('rethinkdb-ts');

/**
 * @typedef {import('discord.js').User} DiscordUser
 */

// [internal]
const Admin = require('./Admin.js');

class User {
  /**
   * @param {DiscordUser} user
   */
  constructor(user) {
    this._client = user.client
    this.user = user;
  }

  get query() {
    return r.table('users').get(this.user.id);
  }

  get r() {
    return r;
  }

  get admin() {
    return new Admin(this.user);
  }
}

module.exports = User; 
```

Enter fullscreen mode Exit fullscreen mode

```
// file: ./src/classes/User/Admin.js

/**
 * @typedef {import('discord.js').User} DiscordUser
 */

class Admin {
  /**
   * @param {DiscordUser} user
   */
  constructor(user) {
    this._client = user.client;
    this.user = user;
  }

  get _() {
    let Seed = require('./');
    return new Seed(this.user);
  }
}

module.exports = Admin; 
```

Enter fullscreen mode Exit fullscreen mode

高效？酷吗？是的，当然

它是做什么的？
(简而言之)这是类文件循环，其中 Admin 类让“种子”文件返回到其预期状态。用户类实例通过两个构造器传递，因此允许更深层次的递归。
这不是单例编程，如果是，我会把类实例传递过去...但当时我想不出来。

更新于 2019/03/21 13:13 GMT London
替换了对 rethinkdb-ts 的模块支持，以实现更好的类型解析。