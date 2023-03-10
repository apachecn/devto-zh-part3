# 2019 年 3 月排名前 20 的 Python Github 库

> 原文：<https://dev.to/daolf/top-20-python-github-repositories-in-march19-527i>

最受欢迎的 Python 回购有哪些？

受 Iren Korkishko 的启发，我决定为我们心爱的 Python 做同样的事情，他为 Javascript 做了大量的工作。

知道谁会登上领奖台吗？

## 我们开始吧:

#### n 20: [熊猫](https://github.com/pandas-dev/pandas) (18.5k ⭐️)

很多人在广泛的数据领域使用 Pandas，它是一个非常简单而强大的数据分析/操作工具。

#### n 19: [算法面试笔记中文](https://github.com/imhuay/Algorithm_Interview_Notes-Chinese) (19.4k ⭐️)

我真的没什么可补充的了。我很想从说中文的读者那里得到一些反馈。

#### n 18: [蟒蛇图案](https://github.com/faif/python-patterns) (19.6k ⭐️)

Python 中设计模式/习惯用法的集合。设计新解决方案时我的首选。

#### n 17: [100 天的 ML 代码](https://github.com/Avik-Jain/100-Days-Of-ML-Code) (20.7k ⭐️)

Siraj Raval 和 Avik Jain 对 ML 的精彩介绍。

#### n 16: [人脸识别](https://github.com/ageitgey/face_recognition) (21.9k ⭐️)

世界上最简单的用于 Python 和命令行的面部识别 api。想想`requests`，不过对于人脸识别。

```
import face_recognition
image = face_recognition.load_image_file("your_file.jpg")
face_locations = face_recognition.face_locations(image) 
```

Enter fullscreen mode Exit fullscreen mode

给你！

#### n 15: [家庭助理](https://github.com/home-assistant/home-assistant) (22k)

将本地控制和隐私放在第一位的开源家庭自动化。惊喜地看到这一个如此接近顶端。

#### n14:[cpython](https://github.com/python/cpython)(229k)】

有趣的是，最著名和最广泛使用的 Python 实现甚至没有进入前十名。如果这不是超级活跃生态系统的标志，那我不知道这是什么。

#### n 13:[python 中的算法](https://github.com/TheAlgorithms/Python) (23.4k ⭐️)

所有算法都用 Python 实现。对于学生、初学者和准备面试的人来说，这是一个非常有用的资源。

#### n 12: [肯定 bot](https://github.com/certbot/certbot) (24.5k)

一个神奇的工具，让您自动启用 HTTPS 在您的网站上与 EFF 的证书和部署让我们加密证书。

#### n11:[【scrappy】](https://github.com/scrapy/scrapy)(31.8k)

Scrapy 是一个用于 Python 的快速高级 web 爬行和抓取框架。它允许你在 20 行代码和几秒钟内抓取数千个网页。

#### n10:[scikit-learn](https://dev.toscikit-learn/scikit-learn)(33.8k)

可能是最著名的机器学习库。Scikit-learn 允许任何人学习 ML，这要感谢它的低入门点和它的非常广泛和发展的社区。由 David Cournapeau 在谷歌代码之夏项目中发起，现在已经维持了 8 年。恭喜你。！！

#### n9:(35.9k⭐️)

欢迎来到 devOps 世界。Ansible 是一个非常简单的 IT 自动化平台，使您的应用程序和系统更容易部署。避免编写脚本或自定义代码来部署和更新您的应用程序——使用一种接近简单英语的语言，使用 SSH 来实现自动化，无需在远程系统上安装代理。

#### n 8: [请求](https://github.com/kennethreitz/requests) (37.6k)

容易，简单和优雅的 API 使请求成为最著名的 Python repos 之一。
我每天都在使用这个包，老实说，我不知道用 Python 做 http 请求的更简单的方法，自己看吧:

```
>>> import requests
>>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
>>> r.status_code
200 
```

Enter fullscreen mode Exit fullscreen mode

给你！

#### n 7: [喀拉斯](https://github.com/keras-team/keras) (39.1k ⭐️)

Keras 是用 Python 编写的开源神经网络库。可以用在 TensorFlow 等 ML 库上面。
自 2017 年以来由 behing Tensorflow 团队支持，Keras 更多地被认为是一个接口，而不是一个纯粹的 ML 库。

#### n 6: [姜戈](https://github.com/django/django) (40.1k ⭐️)

两个最著名的 Python web 框架之一。另一个在下面不远。

#### n 5: [httpie](https://github.com/jakubroztocil/httpie) (40.2k)】

一个比`curl`更简单、更友好的选择，具有直观的 UI、JSON 支持等等。有人试过这个吗？

[![](img/776eb8700daba4f654100fd8b5db3560.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l_x8Y5R2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jakubroztocil/httpie/master/httpie.png)

#### n 4: [操](https://github.com/nvbn/thefuck) (42k)

宏伟的应用程序，纠正你以前的控制台命令。非常非常非常有用。

[![](img/ce22ca7f2d1f1f92e1724a593a186742.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---xVAHNlN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/nvbn/thefuck/master/example.gif)

#### n 3: [Flask](https://github.com/pallets/flask) (42k)

我最喜欢的 Python web 框架进入了前 3 名。有趣的是，直到最近 Flask 才比 Django 更受欢迎。

#### N2:[awsome python](https://github.com/vinta/awesome-python)(64k)

令人惊叹的 Python 框架、库、软件和资源的精选列表。作为新项目的第一个切入点非常有用。

#### n1:T2】tensorlow(123k)

TensorFlow 是一个使用数据流图进行数值计算的开源软件库。最初由谷歌大脑团队开发，供谷歌内部使用，后来在 2015 年开源。

## 结论:

我认为可以肯定地说，Pythonistas 确实热爱 ML、简单的 API 和学习新事物。我必须说，我很惊讶 TensorFlow 以如此大的优势统治了前 20 名。将这类前 20 名与其他语言的前 20 名进行比较将会非常有趣。这可能是另一篇文章的主题。

## 感谢阅读。

如果你喜欢这篇文章，请在评论中告诉我，不要忘了订阅我的时事通讯，还有更多(你还可以免费获得我下一本电子书的第一章)😎).

我最近开始了一个新的系列 [python 技巧](https://dev.to/daolf/beware-of-python-dictget-194k)，我想你可能会喜欢。

如果你喜欢 JS，我刚刚发表了[一些你可能会喜欢的东西](https://dev.to/daolf/things-you-should-know-about-js-events-4k2l)。

如果你喜欢 git，[我会帮你搞定](https://dev.to/daolf/git-series-13-understanding-git-for-real-by-exploring-the-git-director--5bd0)。