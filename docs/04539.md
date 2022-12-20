# 如何为一个需要在浏览器上运行的库(比如 Dexie.js 或者 local feed)编写测试？

> 原文：<https://dev.to/johnson_cor/how-to-write-tests-for-a-library-that-requires-being-run-on-the-browser-like-dexie-js-or-localforage-48k7>

我一直试图采用 TDD 的方法，用 mocha 和 chai 建立一个辅助项目，但最近我一直碰壁。我想使用 local feed，但是我似乎不能编写运行并与 local feed 交互的自动化测试，因为它需要在浏览器上运行。我想包装一个库并编写测试来覆盖我创建的类。有办法做到这一点吗？