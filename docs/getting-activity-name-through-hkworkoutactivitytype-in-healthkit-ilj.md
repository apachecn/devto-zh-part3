# 通过 HealthKit 中的 HKWorkoutActivityType 获取活动名称

> 原文：<https://dev.to/onmyway133/getting-activity-name-through-hkworkoutactivitytype-in-healthkit-ilj>

[![](img/b3c1234bb9cc85021e0cdbda62944757.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9zisppUs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2600/0%2A45nx40PkfU1tmH3B.jpg)

用 HKObjectType.workoutType()获取锻炼数据后，我们得到 [HKWorkoutActivityType](https://developer.apple.com/documentation/healthkit/hkworkoutactivitytype) ，这是一个 enum enum HKWorkoutActivityType:[UInt](https://developer.apple.com/documentation/swift/uint)。从 Swift 4.2 开始，无法将枚举案例作为字符串获取，因为该枚举具有类型 UInt。

虽然会涉及到一些手工操作，但是我们可以构建一个生成器来获取所有的 enum 用例名称。这里我们将编写代码来生成代码，并使用生成的代码来检查所有的值。

在我们的 iOS 应用程序中执行以下 Swift 代码

```
func makeCode(string: String) {
 let pattern = “case \\w*”
 let range = NSMakeRange(0, string.count-1)
 let regex = try! NSRegularExpression(pattern: pattern, options: [])
 regex
 .matches(in: string, options: [], range: range)
 .forEach({ result in
 let start = string.index(string.startIndex, offsetBy: result.range.lowerBound)
 let end = string.index(string.startIndex, offsetBy: result.range.upperBound)
 let substring = String(string[start…end])
 let name = substring
 .replacingOccurrences(of: “case”, with: “”)
 .replacingOccurrences(of: “ “, with: “”)
 .replacingOccurrences(of: “\n”, with: “”)

print(“dictionary[HKWorkoutActivityType.\(name).rawValue] = \”\(name)\””)
 })
} 
```

其中 string 是来自 [HKWorkoutActivityType](https://developer.apple.com/documentation/healthkit/hkworkoutactivitytype) 的所有案例，例如

```
[case archery](https://developer.apple.com/documentation/healthkit/hkworkoutactivitytype/archery)
The constant for shooting archery.

[case bowling](https://developer.apple.com/documentation/healthkit/hkworkoutactivitytype/bowling)
The constant for bowling

[case fencing](https://developer.apple.com/documentation/healthkit/hkworkoutactivitytype/fencing)
The constant for fencing.

[case gymnastics](https://developer.apple.com/documentation/healthkit/hkworkoutactivitytype/gymnastics)
Performing gymnastics.

[case trackAndField](https://developer.apple.com/documentation/healthkit/hkworkoutactivitytype/trackandfield)
Participating in track and field events, including shot put, javelin, pole vaulting, and related sports. 
```

代码所做的是使用正则表达式检查 case 后的所有名称，并构建我们的代码

```
dictionary[HKWorkoutActivityType.americanFootball.rawValue] = “americanFootball”
dictionary[HKWorkoutActivityType.archery.rawValue] = “archery”
dictionary[HKWorkoutActivityType.australianFootball.rawValue] = “australianFootball”
dictionary[HKWorkoutActivityType.badminton.rawValue] = “badminton”
dictionary[HKWorkoutActivityType.baseball.rawValue] = “baseball” 
```

上面用 dictionary 生成的代码包含作为键的 rawValue 和作为值的 enum case 名称。

稍后，当我们获得任何 HKWorkoutActivityType 时，我们可以与此字典进行比较，以找到实际的名称。这比用数字硬编码活动名称要好，因为那些原始值只是实现细节

原帖[https://medium . com/fantageek/getting-activity-name-through-hkworkoutactivitytype-in-health kit-51109 e022 c 33](https://medium.com/fantageek/getting-activity-name-through-hkworkoutactivitytype-in-healthkit-51109e022c33)