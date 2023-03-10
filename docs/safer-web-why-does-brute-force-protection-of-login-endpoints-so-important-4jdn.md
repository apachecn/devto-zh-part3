# 更安全的 web:为什么对登录端点的暴力保护如此重要？

> 原文：<https://dev.to/animir/safer-web-why-does-brute-force-protection-of-login-endpoints-so-important-4jdn>

我们都知道为什么。因为它节省了私人数据和金钱。但这还不是全部。最重要的是，它使互联网总体上更加安全，因此用户可以获得更好的体验，对网络服务更加满意。

不久前，我创建了一个 Node.js 包[速率限制灵活的](https://github.com/animir/node-rate-limiter-flexible)，它提供了许多抵御 DoS 和暴力攻击的工具。我深入这个主题，发现一些 javascript 开源项目不太关心安全性。我不确定其他语言的项目，但我猜是一样的。电商项目很多，也不太在意。

我最近发表了一篇关于暴力保护的文章，并附有分析和示例。你可以在这里阅读完整版[。](https://medium.com/@animirr/secure-web-applications-against-brute-force-b910263de2ab)

这里有一个例子，首先作为一个提醒，我们(开发人员、项目经理、首席执行官等)应该注意它。没时间写多余的代码？别担心，这很容易。

保护的主要思想是风险最小化。登录端点限制允许的请求数量并阻止额外的请求。
我们应该创建两个不同的限制器:

1.  第一个计数连续失败的尝试次数，最多允许 10 次用户名+IP 对。
2.  第二组每天阻止 100 次失败尝试，持续 1 天。

```
const http = require('http');
const express = require('express');
const redis = require('redis');
const { RateLimiterRedis } = require('rate-limiter-flexible');
const redisClient = redis.createClient({
  enable_offline_queue: false,
});

const maxWrongAttemptsByIPperDay = 100;
const maxConsecutiveFailsByUsernameAndIP = 10;

const limiterSlowBruteByIP = new RateLimiterRedis({
  redis: redisClient,
  keyPrefix: 'login_fail_ip_per_day',
  points: maxWrongAttemptsByIPperDay,
  duration: 60 * 60 * 24,
  blockDuration: 60 * 60 * 24, // Block for 1 day, if 100 wrong attempts per day
});

const limiterConsecutiveFailsByUsernameAndIP = new RateLimiterRedis({
  redis: redisClient,
  keyPrefix: 'login_fail_consecutive_username_and_ip',
  points: maxConsecutiveFailsByUsernameAndIP,
  duration: 60 * 60 * 24 * 90, // Store number for 90 days since first fail
  blockDuration: 60 * 60 * 24 * 365 * 20, // Block for infinity after consecutive fails
});

const getUsernameIPkey = (username, ip) => `${username}_${ip}`;

async function loginRoute(req, res) {
  const ipAddr = req.connection.remoteAddress;
  const usernameIPkey = getUsernameIPkey(req.body.email, ipAddr);

  const [resUsernameAndIP, resSlowByIP] = await Promise.all([
    limiterConsecutiveFailsByUsernameAndIP.get(usernameIPkey),
    limiterSlowBruteByIP.get(ipAddr),
  ]);

  let retrySecs = 0;

  // Check if IP or Username + IP is already blocked
  if (resSlowByIP !== null && resSlowByIP.remainingPoints <= 0) {
    retrySecs = Math.round(resSlowByIP.msBeforeNext / 1000) || 1;
  } else if (resUsernameAndIP !== null && resUsernameAndIP.remainingPoints <= 0) {
    retrySecs = Math.round(resUsernameAndIP.msBeforeNext / 1000) || 1;
  }

  if (retrySecs > 0) {
    res.set('Retry-After', String(retrySecs));
    res.status(429).send('Too Many Requests');
  } else {
    const user = authorise(req.body.email, req.body.password);
    if (!user.isLoggedIn) {
      // Consume 1 point from limiters on wrong attempt and block if limits reached
      try {
        const promises = [limiterSlowBruteByIP.consume(ipAddr)];
        if (user.exists) {
          // Count failed attempts by Username + IP only for registered users
          promises.push(limiterConsecutiveFailsByUsernameAndIP.consume(usernameIPkey));
        }

        await promises;

        res.status(400).end('email or password is wrong');
      } catch (rlRejected) {
        if (rlRejected instanceof Error) {
          throw rlRejected;
        } else {
          res.set('Retry-After', String(Math.round(rlRejected.msBeforeNext / 1000)) || 1);
          res.status(429).send('Too Many Requests');
        }
      }
    }

    if (user.isLoggedIn) {
      if (resUsernameAndIP !== null && resUsernameAndIP.consumedPoints > 0) {
        // Reset on successful authorisation
        await limiterConsecutiveFailsByUsernameAndIP.delete(usernameIPkey);
      }

      res.end('authorized');
    }
  }
}

const app = express();

app.post('/login', async (req, res) => {
  try {
    await loginRoute(req, res);
  } catch (err) {
    res.status(500).end();
  }
}); 
```

解封的实现取决于你，有合适的`delete(key)`方法。

本文和[官方文件](https://github.com/animir/node-rate-limiter-flexible/wiki)中的更多例子