# 60 秒内从零到 clojure

> 原文：<https://dev.to/swlkr/go-from-zero-to-clojure-in-60-seconds-160n>

我以前分享过这个，但是我想我应该再写一些关于它的东西，因为事情在过去发生了如此巨大的变化。leiningen 或 boot 曾经是 clojure 开发的首选工具，但是它们很容易混淆，而且经常会有很多不必要的特性。任何 clojure 开发人员都会告诉你，无论如何，简单性应该高于易用性！！不，我是在开玩笑，我不会颂扬简化的优点，也不会就什么是容易或简单进行迂腐的争论。听着，我们就直入主题吧。

## 这很容易

主要的区别在于易用性，进入 clojure 的[入门页面](https://clojure.org/guides/getting_started)，为你的平台安装 clojure。我的平台是并且希望永远是 MacOS，所以这里有一个大的惊喜:

```
brew  install  clojure 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！完成了，您已经安装了 clojure。恭喜你！🎉🍾🎈🎊

用 clj 启动它，你就可以开始在你的终端上输入 clojure 代码，不用大惊小怪...ing。干得好！当然，为 repl 编辑器集成设置它有点困难，但无论如何，这需要一个截屏。坐下来，放松，为自己出色的工作而自我表扬👍

## deps.edn

现在你已经安装了 clojure，让我们用它做一件有用的事情，求和！超级有用！创建一个新目录，添加两个文件和一个文件夹，如下:

```
mkdir -p summer summer/src
cd summer
echo '{:paths ["src"] :deps {org.clojure/clojure {:mvn/version "1.9.0"}}}' >> deps.edn
echo '(ns core) (defn -main [] (println (+ 1 2 3)))' >> src/core.clj 
```

Enter fullscreen mode Exit fullscreen mode

继续像这样运行它

```
clj -m core 
```

Enter fullscreen mode Exit fullscreen mode

它应该输出 6！🎉

这就是现在开始使用 clojure 的全部内容。2019 年可能只是 clojure 接管 dev 世界的一年！

—

原贴于[裸奔. app](https://streaking.app/posts/go-from-zero-to-clojure-in-60-seconds-2b6d20b6118d)