# 如何检查 iOS 中是否实际启用了推送通知

> 原文：<https://dev.to/onmyway133/how-to-check-if-push-notification-is-actually-enabled-in-ios-20o8>

有时我们希望记录用户是否能收到推送通知。我们可能想仅仅使用`isRegisteredForRemoteNotifications`但这还不够。从用户的角度来看，他们可以接收推送通知，也可以不接收。但在幕后，很多因素都在博弈中。可能是用户在 app 设置或 iOS 设置中禁用了推送通知。也可以是用户启用推送通知，但禁用所有声音或横幅显示机制。

`isRegisteredForRemoteNotifications`是你的 app 已经连接到 APNS 并获得设备令牌，这个可以用于静默推送通知
T1 是用于用户权限，没有这个，就没有提醒、横幅或声音推送通知传送到 app
这里是检查

```
static var isPushNotificationEnabled: Bool {
  guard let settings = UIApplication.shared.currentUserNotificationSettings
    else {
      return false
  }

  return UIApplication.shared.isRegisteredForRemoteNotifications
    && !settings.types.isEmpty
} 
```

对于 iOS 10，随着`UserNotifications`框架的引入，你应该使用用户通知框架
，而不是检查`currentUserNotificationSettings`

```
center.getNotificationSettings(completionHandler: { settings in
  switch settings.authorizationStatus {
  case .authorized, .provisional:
    print("authorized")
  case .denied:
    print("denied")
  case .notDetermined:
    print("not determined, ask user for permission now")
  }
}) 
```

推送通知可以通过多种方式发送到我们的应用程序，我们可以要求这样做

```
UNUserNotificationCenter.current()
  .requestAuthorization(options: [.alert, .sound, .badge]) 
```

用户可以在任何时候进入设置应用程序并关闭其中任何一个，所以最好在设置对象
中检查

```
open class UNNotificationSettings : NSObject, NSCopying, NSSecureCoding {

    open var authorizationStatus: UNAuthorizationStatus { get }

    open var soundSetting: UNNotificationSetting { get }

    open var badgeSetting: UNNotificationSetting { get }

    open var alertSetting: UNNotificationSetting { get }

    open var notificationCenterSetting: UNNotificationSetting { get }
} 
```

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️