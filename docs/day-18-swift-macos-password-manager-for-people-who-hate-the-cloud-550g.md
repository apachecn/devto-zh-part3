# 第 18 天:Swift macOS 密码管理器，适用于讨厌云的人

> 原文：<https://dev.to/swlkr/day-18-swift-macos-password-manager-for-people-who-hate-the-cloud-550g>

[<——第 17 天去这里](https://dev.to/swlkr/day-17-swift-macos-password-manager-for-people-who-hate-the-cloud-21an)

📅2019 年 1 月 18 日
🚀距离发布还有 12 天
🔥17 天连胜
💰2.99 美元的价格(现在进入一杯咖啡的领域)
🤑0 美元收入
📈0 顾客
⌚️花了 23 个小时
💻78 个文件被更改，246992 个插入(+)，54 个删除(-)(插入包括第三方代码😬)
🏁今日目标:**触控 ID 支持**

下午 5 点 05 分
我一直在这件事上浪费时间！

**下午 5:30**
touch id 做起来出奇的简单……下面是代码

```
 import LocalAuthentication

    func showTouchID() {
        let context = LAContext()
        context.localizedCancelTitle = "Cancel"
        context.localizedFallbackTitle = "Unlock with password"

        var error: NSError?
        if context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) {
            let reason = "unlock AllYourPasswords"

            context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, localizedReason: reason ) { success, error in

                if success {
                    // Move to the main thread because a state update triggers UI changes.
                    DispatchQueue.main.async { [unowned self] in
                        self.showPasswords()
                    }

                } else {
                    print(error?.localizedDescription ?? "Failed to authenticate")
                }
            }
        }
    } 
```

今晚我可能会做些别的事情，因为那只有 20 分钟…看起来是这样的:

[![](img/8190e273c3234509203bc843ad74d848.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xSfiLkk9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i9no83yi96s1g5q0s41z.gif)

我会回来的🕶

原来今晚我有自己的生活！什么！明天见👉