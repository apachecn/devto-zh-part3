# 使用 Realm 和 Swift5 的高尔夫记分卡

> 原文：<https://dev.to/r_a_chalmers/a-score-card-for-golf-using-realm-and-swift5-5h8j>

故事从这里开始。我在 Dev.to.
的第一篇帖子开始于:20129 年 5 月 5 日。
最后编辑/更新时间:2019 年 5 月 8 日

我正在跟踪我的 Swift + Realm 高尔夫记分卡的发展阶段。

`Part 1.`

我正在建立一个一对多的数据库，记录特定课程的分数。

我目前看到的数据库对象。这第一部分是在我称之为游戏的课上。在这里，我可以有多达 4 名球员保持他们的游戏得分。
所以这就是“多”——这个分数和球员分组是针对某个特定球场的。一对多中的一个。

Game.swift

```
 class Game: Object {
  // Date game played and description or name of course
  @objc dynamic var played = Date()
  @objc dynamic var gameDescription = ""
  @objc dynamic var created = Date()
  // names of up to 4 players
  @objc dynamic var nameA = ""
  @objc dynamic var nameB = ""
  @objc dynamic var nameC = ""
  @objc dynamic var nameD = ""
// hole number and details
  @objc dynamic var holeNumber = 0.0
  @objc dynamic var holeScoreA = 0.0
  @objc dynamic var holeScoreB = 0.0
  @objc dynamic var holeScoreC = 0.0
  @objc dynamic var holeScoreD = 0.0

  @objc dynamic var holePar = 0.0
  @objc dynamic var holeDistance = 0.0
  @objc dynamic var latitude = 0.0
  @objc dynamic var longitude = 0.0

  @objc dynamic var coursename: Course!
  } 
```

到目前为止，课程对象要简单得多。这是一对多排列的第一个元素。

Course.swift

```
class Course: Object {
    @objc dynamic var coursename = ""
} 
```

### 第二部分

同时在 Mapbox 地图上工作来配合这个项目。

https://studio.mapbox.com
的[地图和 https://docs.mapbox.com/help/tutorials/first-steps-ios-sdk/](https://studio.mapbox.com) 的开场教程

我开始使用 Main.storyboard 选项，但由于 XCode 错误的问题，我没有耐心去解决，所以又回到了 Swift 代码版本。无论如何，代码给了我更多的控制权。

在他们网页上的代码中，你会注意到复制元素并不总是与你需要的代码一致。所以全部复制下来。

在第一部分中被贬低的那部分代码，你会在下面的我的代码中看到注释。

简而言之:
这个:

```
let camera = MGLMapCamera(lookingAtCenter: annotation.coordinate, fromDistance: 4500, pitch: 15, heading: 180) 
```

应该是这样:

```
let camera = MGLMapCamera(lookingAtCenter: mapView.centerCoordinate, altitude: 4500, pitch: 15, heading: 180) 
```

```
//
//  ViewController.swift
//  MapBoxTutoriaCodeVersion
//
//  Created by Robert Chalmers on 08/05/2019.
//  Copyright © 2019 R.A.Chalmers. All rights reserved.
//

import UIKit
import Mapbox

class ViewController: UIViewController, MGLMapViewDelegate {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        let url = URL(string: "mapbox://styles/rachalmers/cjvcj22si4bjh1fqugod4sfyh")
        let mapView = MGLMapView(frame: view.bounds, styleURL: url)
        mapView.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        mapView.setCenter(CLLocationCoordinate2D(latitude: 52.088122, longitude: 1.284371), zoomLevel: 16.08, animated: false)
        view.addSubview(mapView)
        mapView.styleURL = MGLStyle.satelliteStyleURL
        // Add a point annotation
        let annotation = MGLPointAnnotation()

        annotation.coordinate = CLLocationCoordinate2D(latitude: 52.088122, longitude: 1.284371)
        annotation.title = "Seckford Golf Club"
        annotation.subtitle = "Suffolk's best golf club!"
        mapView.addAnnotation(annotation)

        // Set the map view's delegate

        mapView.delegate = self

        // Allow the map view to display the user's location
        mapView.showsUserLocation = true
    }

    func mapView(_ mapView: MGLMapView, annotationCanShowCallout annotation: MGLAnnotation) -> Bool {
        // Always allow callouts to popup when annotations are tapped.
        return true
    }

    func mapView(_ mapView: MGLMapView, didSelect annotation: MGLAnnotation) {
        //let camera = MGLMapCamera(lookingAtCenter: annotation.coordinate, fromDistance: 4500, pitch: 15, heading: 180)
        //let camera = MGLMapCamera(lookingAtCenter centerCoordinate: annotation.coordinate, fromDistance: 4500, pitch: 15, heading: 180)
        let camera = MGLMapCamera(lookingAtCenter: mapView.centerCoordinate, altitude: 4500, pitch: 15, heading: 180)
        //'init(lookingAtCenter:fromDistance:pitch:heading:)' is deprecated: Use -cameraLookingAtCenterCoordinate:acrossDistance:pitch:heading: or -cameraLookingAtCenterCoordinate:altitude:pitch:heading:.
        mapView.fly(to: camera, withDuration: 4,
                    peakAltitude: 3000, completionHandler: nil)
    }

    } 
```

但除此之外，到目前为止还不错。第一张地图在这里。 [MapBox 地图](https://api.mapbox.com/styles/v1/rachalmers/cjvcj22si4bjh1fqugod4sfyh.html?fresh=true&title=true&access_token=pk.eyJ1IjoicmFjaGFsbWVycyIsImEiOiJjanZjN3QxODYxNjlwNGRwN3kzb3NhNjVyIn0.UkqUjp5Nttg2YZS94X1ORQ#15.1/52.087815/1.287832/0)