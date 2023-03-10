# Swift 中的数据持久性:用户默认值

> 原文：<https://dev.to/mrcflorian/data-persistence-in-swift-userdefaults-3n8f>

在 iOS 应用程序中有多种方式[保存数据。在本文中，我们将讨论用户默认值。在 Swift 中，UserDefaults API 可用于存储有关应用程序或用户设置的少量信息。](https://iosapptemplates.com/blog/ios-development/data-persistence-ios-swift)

[![swift userdefaults](img/6d1c7d437ee88dd868b263b0a4033615.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3y34DiOR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e003j4goln59vntyl4n6.jpeg)

例如，用户应该能够指定在显示一些股票数据时应该显示的默认回放速度或货币。这些偏好应该是特定于应用程序的，并且应该在应用程序启动之间保持不变。但是，当用户删除应用程序时，这些数据存储会被清除，因此数据在应用程序安装过程中不会持久。

## 用户默认值示例

```
class UserRepository {
    enum Key: String, CaseIterable {
        case name, avatarData
        func make(for userID: String) -> String {
            return self.rawValue + "_" + userID
        }
    }
    let userDefaults: UserDefaults
    // MARK: - Lifecycle
    init(userDefaults: UserDefaults = .standard) {
        self.userDefaults = userDefaults
    }
    // MARK: - API
    func storeInfo(forUserID userID: String, name: String, avatarData: Data) {
        saveValue(forKey: .name, value: name, userID: userID)
        saveValue(forKey: .avatarData, value: avatarData, userID: userID)
    }

    func getUserInfo(forUserID userID: String) -> (name: String?, avatarData: Data?) {
        let name: String? = readValue(forKey: .name, userID: userID)
        let avatarData: Data? = readValue(forKey: .avatarData, userID: userID)
        return (name, avatarData)
    }

    func removeUserInfo(forUserID userID: String) {
        Key
            .allCases
            .map { $0.make(for: userID) }
            .forEach { key in
                userDefaults.removeObject(forKey: key)
        }
    }
    // MARK: - Private
    private func saveValue(forKey key: Key, value: Any, userID: String) {
        userDefaults.set(value, forKey: key.make(for: userID))
    }
    private func readValue<T>(forKey key: Key, userID: String) -> T? {
        return userDefaults.value(forKey: key.make(for: userID)) as? T
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 好处

*   UserDefaults 易于使用，有一个简单干净的 API
*   UserDefaults 是线程安全的(您可以从任何线程读取和写入值)
*   用户默认值在应用程序和应用程序扩展之间共享

## 缺点

*   碰撞很容易发生
*   缺乏数据加密
*   在应用安装中不持久

请继续关注其他文章，在这些文章中，我将介绍一些用户默认值的替代方案，试图减轻上面概述的一些限制。

资源

*   [链接 1](https://pastelink.net/bwy4pq2b)
*   [微](https://micro.blog/sebduta)
*   借方