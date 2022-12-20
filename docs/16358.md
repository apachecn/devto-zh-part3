# 第 10 天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-10-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-1edd>

[<——第 9 天去这里](https://dev.to/swlkr/day-9-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-4koj)

📅2019 年 1 月 10 日
🚀距离发布还有 20 天
🔥九天连胜
🤑0 美元
📈0 顾客
⌚️花了 8 个小时
💻19 个文件被更改，1037 个插入(+)，170 个删除(-)
🏁今天的目标:**编辑现有的登录，希望生成强密码，以及一些 ui 改进**

**下午 2:44**
还在工作，但想早点开始工作。为工作后的生产力做好心理准备。

下午 5:23
修正了一个新登录创建时名字字段没有保存的错误。还将故事板中的 tableview 更改为多选。小小的改变可以让体验发生巨大的变化。

**晚上 11:29**
看着网飞，想到了一个很酷的新方法来写这些日常帖子，摆弄了一会儿 ui，但想不出任何像 1password 7 这样酷的东西。得到了编辑按钮的工作，虽然，可能总共 30 分钟做了今天哈哈。什么是一致性胜过灵感的迸发？

[![](img/8ffbce549178b1bd3ad70d775acf29f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vRPtRsvF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hsvg2860i120s7cgv6mb.png)

过了一会儿，代码开始看起来一样了，我想我已经掌握了 swift 和 macos dev 的窍门。

```
 @IBAction func editButtonClicked(_ sender: NSButton) {
        let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: Bundle.main)
        let vc = storyboard.instantiateController(withIdentifier: "AddLoginViewController") as! AddLoginViewController
        vc.login = login

        let parentVC = self.parent as! PasswordListViewController

        for sView in parentVC.containerView!.subviews {
            sView.removeFromSuperview()
        }

        addChild(vc)
        vc.view.frame = parentVC.containerView!.bounds
        parentVC.containerView!.addSubview(vc.view)
    } 
```

继续前进。