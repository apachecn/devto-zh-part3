# nodejs v11 比 v6 快吗

> 原文：<https://dev.to/qm3ster/is-nodejs-v11-faster-than-v6-3aeh>

本文并不打算提出一个代表任何人的真实世界应用程序的基准(希望如此)。
只是由[这个评论](https://dev.to/qm3ster/comment/8ln7)挑起的一点小事。
它没有以一种有用的方式回答名义上的问题。
如果你有任何有用的比较想让我链接，请在评论中留下。

说真的，这根本不应该影响您的升级决策。

## 我们的“代号”

让我们运行一个简单的测试:

```
"use strict"
for (let i = 0; i < 10; i++) {
  let arr = ["a single pickle"]
  for (let i = 0; i < 25600000; i++) arr = [arr]
  console.log(i)
} 
```

## 结果

让我们从可笑的久远开始:

```
$ nvm use 1 && time node scrap.js 
Now using io.js v1.8.4 (npm v2.9.0)

real    0m38.984s
user    0m36.885s
sys     0m3.073s 
```

哦，是的，一个进步！T3】

```
$ nvm use 4 && time node scrap.js 
Now using node v4.9.1 (npm v2.15.11)

real    0m21.040s
user    0m18.836s
sys     0m2.834s 
```

`nodejs v6` :

```
$ nvm use 6 && time node scrap.js 
Now using node v6.16.0 (npm v3.10.10)

real    0m23.870s
user    0m24.297s
sys     0m2.902s 
```

`nodejs v8` : *回`v4`表现，牛逼！*

```
$ nvm use 8 && time node scrap.js 
Now using node v8.15.0 (npm v6.7.0)

real    0m21.562s
user    0m23.256s
sys     0m2.839s 
```

`nodejs v9`:(一会儿你就知道为什么要考这个非 LTS 了)

```
$ nvm use 9 && time node scrap.js 
Now using node v9.11.2 (npm v5.6.0)

real    0m19.989s
user    0m21.716s
sys     0m2.990s 
```

哦，不，发生了什么事？T3】

```
$ nvm use 10.0.0 && time node scrap.js 
Now using node v10.0.0 (npm v5.6.0)

real    1m14.604s
user    1m22.655s
sys     0m3.232s 
```

`nodejs v10` : **那就更惨了！**T3】

```
$ nvm use 10 && time node scrap.js 
Now using node v10.15.0 (npm v6.6.0)
0

<--- Last few GCs --->
e [22823:0x29e79b0]    18607 ms: Mark-sweep 1278.0 (1334.2) -> 1278.0 (1303.2) MB, 1849.1 / 0.0 ms  (+ 0.0 ms in 69 steps since start of marking, biggest step 0.0 ms, walltime since start of marking 1875 ms) (average mu = 0.075, current mu = 0.052) allocati[22823:0x29e79b0]    20618 ms: Mark-sweep 1339.5 (1365.7) -> 1339.5 (1365.7) MB, 1900.1 / 0.0 ms  (+ 0.0 ms in 5 steps since start of marking, biggest step 0.0 ms, walltime since start of marking 1965 ms) (average mu = 0.064, current mu = 0.055) allocatio

<--- JS stacktrace --->

==== JS stack trace =========================================

    0: ExitFrame [pc: 0x39c8186dbe1d]
Security context: 0x363e2bf1e6e1 <JSObject>
    1: /* anonymous */ [0x2b128b88e9f1] [/home/mihail/Documents/Development/zigbee/zcl-packet/src/scrap.js:~1] [pc=0x39c81876f729](this=0x2b128b88eb21 <Object map = 0x389f21f02571>,exports=0x2b128b88eb21 <Object map = 0x389f21f02571>,require=0x2b128b88eae1 <JSFunction require (sfi = 0x170c23bc6179)>,module=0x2b128b88ea59 <Module map = 0x389f21f50c21>,__filen...

FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory
 1: 0x8db900 node::Abort() [node]
 2: 0x8db94c  [node]
 3: 0xad6c1e v8::Utils::ReportOOMFailure(v8::internal::Isolate*, char const*, bool) [node]
 4: 0xad6e54 v8::internal::V8::FatalProcessOutOfMemory(v8::internal::Isolate*, char const*, bool) [node]
 5: 0xec44e2  [node]
 6: 0xec45e8 v8::internal::Heap::CheckIneffectiveMarkCompact(unsigned long, double) [node]
 7: 0xed06c2 v8::internal::Heap::PerformGarbageCollection(v8::internal::GarbageCollector, v8::GCCallbackFlags) [node]
 8: 0xed0ff4 v8::internal::Heap::CollectGarbage(v8::internal::AllocationSpace, v8::internal::GarbageCollectionReason, v8::GCCallbackFlags) [node]
 9: 0xed3c61 v8::internal::Heap::AllocateRawWithRetryOrFail(int, v8::internal::AllocationSpace, v8::internal::AllocationAlignment) [node]
10: 0xe9d0e4 v8::internal::Factory::NewFillerObject(int, bool, v8::internal::AllocationSpace) [node]
11: 0x113c9db v8::internal::Runtime_AllocateInTargetSpace(int, v8::internal::Object**, v8::internal::Isolate*) [node]
12: 0x39c8186dbe1d 
Aborted (core dumped)

real    0m22.973s
user    0m25.113s
sys     0m0.655s 
```

相同，但是在将数组深度减少到`25500000` : **明显比`v10.0.0`慢，即使要做的工作更少！**

```
$ nvm use 10 && time node scrap.js 
Now using node v10.15.0 (npm v6.6.0)

real    1m49.499s
user    2m0.444s
sys     0m3.553s 
```

撞回`25600000`、`nodejs v11` : **还好，至少没撞上**

```
$ nvm use 11 && time node scrap.js 
Now using node v11.9.0 (npm v6.5.0)

real    1m52.919s
user    2m5.220s
sys     0m3.600s 
```

## 结论

没有可操作的结论。这种荒谬的情况，我们当然不应该希望 V8 或 nodejs 开发团队进行优化，随着`10.0.0`的发布，这种情况已经显著恶化，并且没有好转的迹象。哦，好吧。继续我们的生活。

## 浏览器

*   所提供的代码在 Chromium 71.0.3578.98 中的运行速度与 nodejs v4 相当或更快。

    我不知道该怎么想。

*   在 Firefox 65.0 中，它的运行速度比 v10 快，但比 v6 慢。

    如果我禁用它，询问“一个页面使 Firefox 变慢，你想要停止它吗？”看门狗。如果有人知道什么旗帜可以做到这一点，请告诉我。

## 免责声明

这并不是建议推迟切换到 nodejs v10。我的建议是:如果你发现有人在使用`< v10`请冷静，不要牺牲专业精神，温和，非暴力，但坚持打断他们的腿。
这里有许多可用性和安全性的含义，这个“基准”绝不是真实应用的代表。
(在运行正常代码和 IO 等本机方面都有性能提升)