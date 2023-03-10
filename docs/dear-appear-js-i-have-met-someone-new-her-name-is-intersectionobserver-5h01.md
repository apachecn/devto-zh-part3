# 亲爱的 appear.js，我认识了一个新的人。她的名字叫交叉观察者。

> 原文：<https://dev.to/btdev/dear-appear-js-i-have-met-someone-new-her-name-is-intersectionobserver-5h01>

[![appear.js IntersectionObserver Meme](img/d3560d77e8545574998fe54c5c837bed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YHGenCqU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2lmg5c5gnm74vdbq332a.jpg)

亲爱的 [appear.js](https://creativelive.github.io/appear/) ，我认识了一个新的人。和你在一起或多或少是一段美好的时光。但是我想分手，你必须离开你的堆栈。

新的那个？她叫交叉观察者。她对我很好。她很聪明，语法漂亮，做事很快。我想我会和她过得很愉快。

玩笑开够了。在 blue-tomato.com，我们使用了一些[基于 jQuery 的图像延迟加载库](https://appelsiini.net/projects/lazyload/v1/)和 [appear.js](https://creativelive.github.io/appear/) 来基于视点中的元素进行延迟加载和效果处理。

两个库都监听滚动事件，并查看实际的滚动位置是否在给定元素位置的范围内。

这个技术在很长一段时间里都是默认的，但是自从 IntersectionObserver 出现后，很明显有时会切换到这个技术。旧方法的主要问题是滚动事件列表对浏览器性能的影响非常大。

## 举例

### 用 appear.js 制作一个有粘性的导航栏

在这个例子中，在`.main-content`变得可见后，`.navigation`栏将获得 CSS 类`.navigation--sticky`，并变得有粘性。当`.main-content`消失时(例如，通过滚回页面顶部)，通过移除`.navigation--sticky`类，`.navigation`栏将变为静态。

```
appear({
  elements: function elements() {
    return document.querySelectorAll('.main-content');
  },
  appear: function appear() {
    $(".navigation").addClass('.navigation--sticky');
  },
  disappear: function appear() {
    $(".navigation").removeClass('.navigation--sticky');
  }
}); 
```

### 用 InteractionObserver 制作有粘性的导航栏

对于 InteractionObserver，这将按如下方式工作:

```
var element = document.querySelector('.main-content');
if(element) {
  let observer = new IntersectionObserver(entries => {
    entries.forEach(element => {
      if(element.intersectionRatio > 0) {
        $(".navigation").addClass('.navigation--sticky');
      } else {
        $(".navigation").removeClass('.navigation--sticky');
      }
    });
  }, { rootMargin: '0px 0px', threshold: 0.00 });

  observer.observe(element);
} 
```

## 什么是根缘和阈值？

Appear.js 只有一些“bounds”选项，让它在元素真正出现在视口中之前激发一些像素。IntersectionObserver 通过 root/rootMargin 和 thresold 选项提供了一个非常强大和灵活的方法来控制这一点。看看这篇来自 Smashing Magazine 的文章，Denys Mishonnuc 对此解释得非常详细:[https://www . smashingmagazine . com/2018/01/defer ing-lazy-loading-intersection-observer-API/# intersection observer-initialization](https://www.smashingmagazine.com/2018/01/deferring-lazy-loading-intersection-observer-api/#intersectionobserver-initialization)