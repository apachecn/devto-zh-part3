# 了解《拉茜福音》

> 原文：<https://dev.to/armariya/-lazy-evaluation-1n4n>

[![](img/0563dae91492b6886418763c070570f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--coev00vC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/325/1%2Akj2K9j5jjwTWiCdm6XUqlQ.gif)

我不知道有多少人听说过这个词，但我最近从读《t0㎡book》、《信托计划》中听说过。

#### 模糊均衡是什么？

“Lazy”是一个非常不可思议的模式。Lazy Evaluation 只是在我们需要它的时候做一些计算。它强调

```
fn simulated\_expensive\_calculation(result: u32) -> u32 {
  thread::sleep(Duration::from\_secs(2));
  println!("Finished");
  result
} 
```

它是一个计算密集的函数模拟，它需要两秒钟。如果我们频繁地调用这个函数，然后调用它，那么不使用这个值将会导致结果。

Lazy Evaluation 通常会和 mememorization 配对，也就是说，当我们计算数值时，我们会记住它。如果参数是相同的，我们会记住它。

#### [t1㎡如何做模糊均衡](#%E0%B8%A7%E0%B8%B4%E0%B8%98%E0%B8%B5%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%97%E0%B8%B3-lazy-evaluation)

现在让我们把上面的集合代码更新成 Lazy Evaluation + Memorization，让我们从函数中改变上面的集合代码开始。

```
let expensive\_closure = |result: u32| -> u32 {
  thread::sleep(Duration::from\_secs(2));
  println!("Finished");
  result
}; 
```

在这里，我们还没有工作，只是把函数放在一个无动于衷的变量中。这里没有计算任何东西。我们将创建它。

```
struct Cacher<T> where T: Fn(u32) -> u32 {
  calculation: T,
  values: HashMap<u32, u32>
}

impl<T> Cacher<T> where T: Fn(u32) -> u32 {
  fn new(calculation: T) -> Cacher<T> {
    Cacher {
      calculation,
      values: HashMap::new(),
    }
  }

  fn value(&mut self, arg: u32) -> u32 {
    match self.value.get(&arg) {
      Some(&v) => v,
      None => {
        let v = (self.calculation)(arg);
        self.value.insert(arg, v);
        v
      }
    }
  }
} 
```

可以看出，这个构造有两个变量:一个集合，它是一个闭包，它与一个在正则化和 result non 之间被映射的值一起工作。

从现在开始，是时候了，对吧？它会变成

。

```
let cacher = Cacher::new(|result: u32| -> u32 {
  thread::sleep(Duration::from\_secs(2));
  println!("Finished");
  result
}) 
```

在完成 bup 之前先建立一个快取记忆体。时间会用在 t0㎡上。

```
cacher.value(20) 
```

就像这样，它会跑到上面的功能区，如果它已经处理过这个参数，它会把它反过来，但是如果它还没有包含 get 的话。

我们可以看到它非常有用。它可以让我们的性能更好，但是从上面的实现数据中可以看出，所以我们先来检查一下自己的问题。

没有好，到了广告时间，现在要重新打开播客了。这个名字叫做“深度魔术”。其来源是，Wikipedia 上的这个单词是这样的。

> **深幻**指不广为人知的技术，可能被刻意保密。

所以我对这个词很感兴趣。我要做一个简短的播客，把在工作中遇到的 Technique 放在一起分享。FM/ariya-lawanitchanon。其他频道会逐渐跟进，因为当 submit 没有通过的时候，(ha)就是这样。