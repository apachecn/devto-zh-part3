# 提高 CLLocationManager 的可测试性

> 原文：<https://dev.to/apium_hub/improving-testability-of-cllocationmanager-3d1g>

当我们编写具有外部依赖性的类测试时，发现不属于我们的方法的响应有困难是很常见的。让我们来看看[移动开发](https://apiumhub.com/mobile-app-development-barcelona/)中的一个典型案例，在这里我们可以发现这个问题，并找出我们如何提高 CLLocationManager 的可测试性。

## 提高 CLLocationManager 的可测试性

我们已经开发了一个应用程序，它有一个地图，并使用苹果 *CoreLocation* 的定位服务来获取用户的位置。

我们已经说过，当我们的类使用 *CoreLocation 框架*时，问题就来了。

当调用 *CLLocationManager* 类的 *requestLocation ()* 方法时，它试图检索用户的位置，并调用我们的委托方法，该方法在*CLLocationManager delegate*委托所符合的类中。

我们的目的是能够控制由 requestLocation()等方法生成的值，所有这些通过委托进行的通信使得这个过程更加困难。

让我们通过使用协议模拟 CLLocationManager 的外部接口来看看如何做到这一点。

我们从我们的定位服务类开始，我们称之为 *LocationService* 。

它是一个与 CLLocationManager 有依赖关系的类，反过来也形成了它的协议*(CLLocationManager delegate)*用于它们之间的通信。

基本上这个类有 *getCurrentLocation* 方法，它现在向框架请求用户的位置，我们在委托中显示它。

```
 class LocationService: NSObject {

    let locationManager: CLLocationManager
    init(locationManager: CLLocationManager = CLLocationManager()) {
        self.locationManager = locationManager
        super.init()
        self.locationManager.desiredAccuracy = kCLLocationAccuracyHundredMeters
        self.locationManager.delegate = self
    }
    func getCurrentLocation() {
        self.locationManager.requestLocation()
        self.locationManager.requestWhenInUseAuthorization()
    }
}

extension LocationService: CLLocationManagerDelegate {

    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations:[CLLocation]) {
        guard let location = locations.first else { return }
        print("The location is: \(location)")
    }
    func locationManager(_ manager: CLLocationManager, didFailWithError error: Error) {
        print("Error: \(error)")
    }
} 
```

从我们的 *LocationService* 类开始，我们要做的第一件事是改变我们的 *getCurrentLocation* 方法返回位置的方式。为此，我们将声明一个回调，我们将在我们的方法中使用它来返回位置。

```
 private var currentLocationCallback: ((CLLocation) -> Void)?

    func getCurrentLocation(completion: @escaping (CLLocation) -> Void) {

        currentLocationCallback = { (location) in
            completion(location)
        }
        …
} 
```

在委托方法中，我们将调用新的回调函数。

```
 func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        guard let location = locations.first else { return }
        self.currentLocationCallback?(location)
        self.currentLocationCallback = nil
    } 
```

正如我们之前说过的，我们的目标是控制 CLLocationManager 返回的位置值，我们将编写一个测试来验证它。

```
 func test_getCurrentLocation_returnsExpectedLocation() {

        let sut = LocationService()
        let expectedLocation = CLLocation(latitude: 10.0, longitude: 10.0)
        let completionExpectation = expectation(description: "completion expectation")

        sut.getCurrentLocation { (location) in
            completionExpectation.fulfill()
            XCTAssertEqual(location.coordinate.latitude,expectedLocation.coordinate.latitude)
            XCTAssertEqual(location.coordinate.longitude,expectedLocation.coordinate.longitude)
        }
        wait(for: [completionExpectation], timeout: 1)
    } 
```

测试失败是因为逻辑上它正在获得设备的实际位置。我们仍然没有办法注入我们希望它返回的价值。

为了解决这个问题，我们将首先创建一个接口，该接口将具有与 *CLLocationManager* 类所需的相同方法。

```
 protocol LocationManagerInterface {
    var locationManagerDelegate: CLLocationManagerDelegate? { get set }
    var desiredAccuracy: CLLocationAccuracy { get set }
    func requestLocation()
    func requestWhenInUseAuthorization()
} 
```

在我们的类中，我们必须将 *CLLocationManager* 的引用改为我们的新接口 *LocationManagerInterface* 的引用。

```
 var locationManager: LocationManagerInterface

    init(locationManager: LocationManagerInterface = CLLocationManager()) {
        self.locationManager = locationManager
        super.init()
        self.locationManager.desiredAccuracy = kCLLocationAccuracyHundredMeters
        self.locationManager.locationManagerDelegate = self
    } 
```

构造函数的默认参数不会让我们改变它，它会警告我们, *CLLocationManager* 类不符合我们已经创建的新接口。

我们在 CLLocationManager 类中添加了一个符合我们的协议的扩展来解决这个问题。

```
extension CLLocationManager: LocationManagerInterface {} 
```

我们的 *LocationManagerInterface* 接口有问题，这是因为我们仍然耦合到核心定位系统委托*CLLocationManagerDelegate*。

为了摆脱这种依赖，我们将创建自己的委托。

```
 protocol LocationManagerDelegate: class {
    func locationManager(_ manager: LocationManagerInterface, didUpdateLocations locations: [CLLocation])
    func locationManager(_ manager: LocationManagerInterface, didChangeAuthorization status: CLAuthorizationStatus)
} 
```

它将有两个非常类似于已经由 *CLLocationManager* 类提供的方法。

我添加了 *didChangeAuthorization* 的方法作为示例，以显示该位置可以被模仿，我们也可以用其他可用的方法来模仿。

现在，我们可以用刚刚创建的接口来更改接口的*CLLocationManagerDelegate*。

编译器会再次警告我们不符合接口。

所以我们分配委托 getters 和 setters 来返回我们的委托，但是仍然使用 CoreLocation 系统委托来获取数据。

```
 extension CLLocationManager: LocationManagerInterface {
    var locationManagerDelegate: LocationManagerDelegate? {
        get { return delegate as! LocationManagerDelegate? }
        set { delegate = newValue as! CLLocationManagerDelegate? }
    }
} 
```

现在几乎所有的东西都连接好了，我们必须将 CLLocationManager 协议信息更改为我们的协议。

我们将分两步进行。

首先，我们根据新的委托创建我们的 *LocationService* 类。

```
 extension LocationService: LocationManagerDelegate {
    func locationManager(_ manager: LocationManagerInterface, didUpdateLocations locations: [CLLocation]) {
        guard let location = locations.first else { return }
        self.currentLocationCallback?(location)
        self.currentLocationCallback = nil
        ...
    } 
```

第二，瘦的那个，我们已经有了 *CoreLocation* ，现在将简单地调用我们创建的那个。

```
 extension LocationService: CLLocationManagerDelegate {

    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        self.locationManager(manager, didUpdateLocations: locations)
    }
    ...
} 
```

回到我们的测试文件，因为我们现在有一个我们控制的类，我们可以创建一个模拟并覆盖我们想要注入的值。

```
 struct LocationManagerMock: LocationManagerInterface {
        var locationManagerDelegate: LocationManagerDelegate?

        var desiredAccuracy: CLLocationAccuracy = 0

        var locationToReturn:(()->CLLocation)?
        func requestLocation() {
            guard let location = locationToReturn?() else { return }
            locationManagerDelegate?.locationManager(self, didUpdateLocations: [location])
        }
    } 
```

我们对初始测试进行必要的修改，以最终通过测试。

```
 func test_getCurrentLocation_returnsExpectedLocation() {
        // 1
        var locationManagerMock = LocationManagerMock()

        // 2
        locationManagerMock.locationToReturn = {
            return CLLocation(latitude: 10.0, longitude: 10.0)
        }
        // 3
        let sut = LocationService(locationManager: locationManagerMock)

        let expectedLocation = CLLocation(latitude: 10.0, longitude: 10.0)
        let completionExpectation = expectation(description: "completion expectation")

        sut.getCurrentLocation { (location) in
            completionExpectation.fulfill()
            XCTAssertEqual(location.coordinate.latitude,expectedLocation.coordinate.latitude)
            XCTAssertEqual(location.coordinate.longitude,expectedLocation.coordinate.longitude)
        }
        wait(for: [completionExpectation], timeout: 1)
    } 
```

1:我们创建新模拟的实例。

2:当执行 *requestLocation ()* 方法时，我们传递想要返回的值。

3:我们将模拟注入到*位置服务*的创建中。回想一下，在 LocationService 的 init 中，我们根据我们的接口而不是特定的类(在本例中是 *CLLocationManager* )来反转位置管理器的依赖关系。

同样，如果我们需要控制本地化许可的授权值，我们也可以用同样的方式，将我们想要覆盖的方法的签名添加到我们的接口中。

如果您有兴趣了解更多关于 CLLocationManager 或移动应用程序开发的信息，我强烈建议您点击[此处](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果您觉得这篇关于 CLLocationManager 的文章很有趣，您可能会喜欢…

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[iOS 快照测试](https://dev.to/apium_hub/ios-snapshot-testing-2i79)

[如何用 MoyaRx 和 Codable 简化数据层](https://dev.to/apium_hub/how-to-simplify-the-data-layer-with-moyarx-and-codable-2eco)

[浓缩咖啡测试](https://dev.to/apium_hub/introduction-espresso-testing-pa9-temp-slug-6400625)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

提高 CLLocationManager 可测试性的帖子[最早出现在](https://apiumhub.com/tech-blog-barcelona/improving-testability-cllocationmanager/) [Apiumhub](https://apiumhub.com) 上。