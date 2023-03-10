# P5-入侵者

> 原文：<https://dev.to/mouseannoying/p5-invader-39cl>

[https://repl.it/@annoyingmouse/p5-INVADER?lite=true](https://repl.it/@annoyingmouse/p5-INVADER?lite=true)

你可能已经看过[以前的](http://drmsite.blogspot.com/2019/03/p5-tail.html) [关于我从](http://drmsite.blogspot.com/2019/03/p5-explosions.html)[线框杂志](https://wireframe.raspberrypi.org/)将 Python 转换成 p5 的帖子，这是迄今为止我尝试过的最难的一个，因为它涉及到大量的数组循环。数组不仅包括组成角色的元素，比如射击和盾牌，还包括我用来生成射击和盾牌图像的矩阵。以这种方式使用矩阵或多维数组是我正在考虑的一种适应[数字计数器](http://drmsite.blogspot.com/2019/03/digital-counter.html)的方法，因为它允许一个数组中的字符的可视化表示，从而使调整字符更容易实现。

如果你查阅一下[回购](https://github.com/annoyingmouse/WireFrameJS/tree/master/009-INVADERS)的历史，你会发现我用了许多不同的方法来计算一发子弹是否只是擦过了一个护盾，或者是否有实质性的影响，导致了爆炸。我最终做到了这一点，允许射击有一个生命值，每次它从盾牌上咬下一个像素，生命值就会增加；当撞击点达到一个临界水平时，子弹就会爆炸，并从护盾上腐蚀掉一大块。

为了节省资源，当一个镜头落到舞台下面太远时，它会被从一个阵列中移除，当它是空的时，每个盾牌都会被移除。我想我也应该在没有更多的屏蔽时关闭处理，但这几乎是我想要的，并且似乎足够接近地模仿了最初的 [Python 实现](https://github.com/Wireframe-Magazine/Wireframe-9)。如果没有，欢迎您进行分支和调整。