# 第 11 天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-11-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-2fln>

[<——第 10 天去这里](https://dev.to/swlkr/day-10-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-1edd)

📅2019 年 1 月 11 日
🚀距离发射还有 19 天
🔥10 天连胜
💰29 美元的价格
🤑0 美元
📈0 客户
⌚️花费了 8.2 小时
💻19 个文件被更改，1048 个插入(+)，170 个删除(-)
🏁今天的目标:**更新现有的登录，希望生成强密码，以及一些 ui 改进**

我需要变得更擅长讲故事[，这样我就可以推销媒体网站](https://campfirelabs.co/blog-1/2019/1/8/the-art-of-pitching-how-i-got-published-in-the-atlantic)来获得点击量并销售这个东西。从现在开始。

### 极短篇小说模式

在 10 天的时间里，我在这个密码管理器上取得了巨大的进步。我的每日帖子很快成为开发者读者群中的主要内容，每篇帖子吸引的浏览量不到 100 次！这可能令人震惊，但是每天在一个副业项目上取得少量的进展真的可以工作！我认为更多的人(不仅仅是我)期待着这个东西的发布，这是对我刚刚向世界介绍的大多数项目的巨大改进，没有从第一天开始谈论开发过程。

是的，那仍然不是很好，但是你知道，这是一个开始。很难讲一个关于 10 个平凡日子的故事。

### 时间轴模式

**下午 5:09**
一开始表现强劲，打开了 xcode，这是在 mac 应用程序上取得一些进展的良好开端。

下午 5:40
在编辑密码和导航回另一个视图控制器时发现了一个错误。这里的这个

```
let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: Bundle.main)
        let vc = storyboard.instantiateController(withIdentifier: "AddLoginViewController") as! AddLoginViewController
        vc.row = login

        let parentVC = self.parent as! PasswordListViewController

        for sView in parentVC.containerView!.subviews {
            sView.removeFromSuperview()
        }

        addChild(vc)
        vc.view.frame = parentVC.containerView!.bounds
        parentVC.containerView!.addSubview(vc.view) 
```

大部分是正确的，除了我将父视图设置为当前视图控制器，所以当我使用容器视图时，我不能返回到包含容器视图的视图控制器。我不得不这样做:

```
parentVC.addChild(vc) 
```

这就是进步，伙计们。更新密码起作用了！🎉请看下面的实际操作👇
[![](img/bbf88578df79d4b14586532cb5501c74.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--4L5UJgUQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3qvspyucj0cvrehjy59f.gif)

不，我的密码不是密码 123