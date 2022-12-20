# 如何在 iOS 中使用谷歌地图进行聚类

> 原文：<https://dev.to/onmyway133/how-to-do-clustering-with-google-maps-in-ios-5on>

## 基本用谷歌地图

添加到 Podfile `pod 'GoogleMaps'`

添加自定义标记

```
import GoogleMaps

final class StopMarker: GMSMarker {
    let stop: Stop

    init(stop: Stop) {
        self.stop = stop
        super.init()
        self.title = stop.name
        self.position = stop.toCoordinate()

        let imageView = UIImageView(frame: CGRect(x: 0, y: 0, width: 30, height: 30))
        imageView.layer.cornerRadius = 15
        imageView.image = UIImage(named: "pin")
        self.iconView = imageView
    }
} 
```

显示标记

```
func handle(stops: [Stop]) {
    self.stops = stops
    stops
        .map({ StopMarker(stop: $0) })
        .forEach {
            $0.map = mapView
        }

} 
```

分配`$0.map = mapView`意味着告诉`GMSMapView`开始渲染标记

手柄龙头

```
extension ViewController: GMSMapViewDelegate {
    func mapView(_ mapView: GMSMapView, didTap marker: GMSMarker) -> Bool {
        zoomIn(coordinate: marker.position)
        return false
    }

    func mapView(_ mapView: GMSMapView, didTapInfoWindowOf marker: GMSMarker) {
        guard let stopMarker = marker as? StopMarker else {
            return
        }

        let detailViewController = StopDetailViewController(stop: stopMarker.stop)
        presentPanModal(detailViewController)
    }
} 
```

## 聚类

按照[https://github . com/Google maps/google-maps-ios-utils/blob/master/swift . MD](https://github.com/googlemaps/google-maps-ios-utils/blob/master/Swift.md)手动添加 Google-maps-IOs-utils

否则，对于 CocoaPods，我们会得到错误

```
[!] The 'Pods-MyApp' target has transitive dependencies that include static binaries: (/Users/khoa/Projects/MyApp/Pods/GoogleMaps/Base/Frameworks/GoogleMapsBase.framework, /Users/khoa/Projects/MyApp/Pods/GoogleMaps/Maps/Frameworks/GoogleMaps.framework, and /Users/khoa/Projects/MyApp/Pods/GoogleMaps/Maps/Frameworks/GoogleMapsCore.framework) 
```

添加`ClusterItem`

```
import Foundation
import CoreLocation

class ClusterItem: NSObject, GMUClusterItem {
    let position: CLLocationCoordinate2D
    let stop: Stop

    init(stop: Stop) {
        self.stop = stop
        self.position = stop.toCoordinate()
    }
} 
```

设置集群管理器

```
let iconGenerator = GMUDefaultClusterIconGenerator()
let algorithm = GMUNonHierarchicalDistanceBasedAlgorithm()
let renderer = GMUDefaultClusterRenderer(mapView: mapView, clusterIconGenerator: iconGenerator)
renderer.delegate = self
clusterManager = GMUClusterManager(map: mapView, algorithm: algorithm, renderer: renderer)
clusterManager.setDelegate(self, mapDelegate: self) 
```

默认算法`GMUNonHierarchicalDistanceBasedAlgorithm`

```
 A simple clustering algorithm with O(nlog n) performance. Resulting clusters are not
 * hierarchical.
 * High level algorithm:
 * 1\. Iterate over items in the order they were added (candidate clusters).
 * 2\. Create a cluster with the center of the item.
 * 3\. Add all items that are within a certain distance to the cluster.
 * 4\. Move any items out of an existing cluster if they are closer to another cluster.
 * 5\. Remove those items from the list of candidate clusters.
 * Clusters have the center of the first element (not the centroid of the items within it). 
```

制作`ClusterItem`并添加到`clusterManager`。最后，叫`clusterManager.cluster()`T3

```
func handle(stops: [Stop]) {
    self.stops = stops
    stops
        .map({ StopMarker(stop: $0) })
        .forEach {
            let item = ClusterItem(stop: $0.stop)
            clusterManager.add(item)
        }

    clusterManager.cluster()
} 
```

默认情况下，clusterManager 使用渲染器来呈现默认大头针标记。实现`GMUClusterRendererDelegate`使用我们的自定义`StopMarker`

```
extension ViewController: GMUClusterRendererDelegate {
    func renderer(_ renderer: GMUClusterRenderer, markerFor object: Any) -> GMSMarker? {
        switch object {
        case let clusterItem as ClusterItem:
            return StopMarker(stop: clusterItem.stop)
        default:
            return nil
        }
    }
} 
```

最后处理点击集群和集群项目

```
extension ViewController: GMUClusterManagerDelegate {
    func clusterManager(_ clusterManager: GMUClusterManager, didTap cluster: GMUCluster) -> Bool {
        print("tap cluster")
        return false
    }

    func clusterManager(_ clusterManager: GMUClusterManager, didTap clusterItem: GMUClusterItem) -> Bool {
        print("tap cluster item")
        return false
    }
} 
```

原帖[https://github.com/onmyway133/blog/issues/191](https://github.com/onmyway133/blog/issues/191)