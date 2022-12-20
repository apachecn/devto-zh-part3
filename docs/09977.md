# 🐍在 Python 中像专家一样记录循环进度

> 原文：<https://dev.to/daolf/logging-loop-progress-like-a-pro-in-python-c6l>

或者 for 循环中的`print(i)`的结尾。

如果你曾经写过这样的东西:

```
 for i in range(0,10000):
         compute(i)
         print(i) 
```

Enter fullscreen mode Exit fullscreen mode

为了控制你的执行进度，这篇短文是给你的🙂。

## All hail tqdm

tqdm 来自阿拉伯语“taqadum ”,意思是“进步”,它是众多 Python 库之一，既有用又易于使用。使用 pip 安装 tqdm，然后按照通常的方式进行安装:

[![Voilà !](img/c742462adcff598620760b54a00eed74.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--K4RfAQrr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AJe75evOTagxyI7R6SdXQoA.gif) *就这样！*

您甚至可以让它与循环中的循环一起工作:

[![Voilà! Voilà!](img/8dea1b5eb1b058c6906f463e2e04f0fe.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--EbkuBs2Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ag3Z_kd_2cjstziQ4z3Sbag.gif) *就这样！给你！*

## 就是这样。

我希望这个库能帮助你，希望你今天能学到一些东西。整个文档可在处[获得。](https://pypi.org/project/tqdm/)

有许多选项可以帮助你制作出你想要的进度条。tqdm 在 Jupyter 笔记本上也很好用，只是做`from tqdm import tqdm_notebook`而不是`from tqdm import tqdm`。

## 感谢阅读

我希望这篇短文能帮助你更好地记录循环，并给你的同事留下深刻印象。

如果你喜欢这篇文章，请在评论中告诉我，不要忘了订阅我的时事通讯，还有更多(你还可以免费获得我下一本电子书的第一章)😎).

如果你喜欢 JS，我刚刚发表了[一些你可能会喜欢的东西](https://dev.to/daolf/things-you-should-know-about-js-events-4k2l)。

如果你喜欢 git，[我会帮你搞定](https://dev.to/daolf/git-series-13-understanding-git-for-real-by-exploring-the-git-director--5bd0)。