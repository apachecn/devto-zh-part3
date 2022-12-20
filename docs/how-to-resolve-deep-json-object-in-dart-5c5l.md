# 如何在 Dart 中解析深层 json 对象

> 原文：<https://dev.to/onmyway133/how-to-resolve-deep-json-object-in-dart-5c5l>

如果我们不在 GRPC 和协议缓冲区的边缘，那么我们很可能会处理 Restful 和 JSON。在我的一个 [Flutter](https://speakerdeck.com/onmyway133/getting-started-with-flutter) 应用中，我需要使用 JSON

## JSON 和序列化

https://flutter.dev/docs/development/data-and-backend/json 的导游绝对值得一去。

目前有两种方法。一种是手动使用`dart:convert`包

```
Map<String, dynamic> user = jsonDecode(jsonString); 
```

Enter fullscreen mode Exit fullscreen mode

另一种方式是使用 [json_serializable](https://pub.dartlang.org/packages/json_serializable) 生成解析代码

```
import 'package:json_annotation/json_annotation.dart';

/// This allows the `User` class to access private members in
/// the generated file. The value for this is *.g.dart, where
/// the star denotes the source file name.
part 'user.g.dart';

/// An annotation for the code generator to know that this class needs the
/// JSON serialization logic to be generated.
@JsonSerializable()

class User {
  User(this.name, this.email);

  String name;
  String email;

  /// A necessary factory constructor for creating a new User instance
  /// from a map. Pass the map to the generated `_$UserFromJson()` constructor.
  /// The constructor is named after the source class, in this case User.
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);

  /// `toJson` is the convention for a class to declare support for serialization
  /// to JSON. The implementation simply calls the private, generated
  /// helper method `_$UserToJson`.
  Map<String, dynamic> toJson() => _$UserToJson(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

## json_resolve

手动方法问题在于它涉及大量样板代码，尤其是在访问深度嵌套的 json 内部的属性时。代码生成方法的问题是，它并不总是符合我们的需要，而且可能缺乏定制。

因此，我创建了 [json_resolve](https://github.com/onmyway133/json_resolve) ，它允许我们使用 keypath 访问 json，并考虑到了类型检查和安全性。代码很小，推理简单，经过测试。

```
final String byProperty = resolve(json: json, path: "movie", defaultValue: "error");
expect(byProperty, "isFun");

final int byInt = resolve(json: json, path: "earth", defaultValue: 0);
expect(byInt, 199999);

final String byIndex = resolve(json: json, path: "dc.2.name", defaultValue: "error");
expect(byIndex, "Wonder Woman");

final String byIndexThenProperty = resolve(json: json, path: "marvel.0.appear.1.title", defaultValue: "error");
expect(byIndexThenProperty, "The Dark World"); 
```

Enter fullscreen mode Exit fullscreen mode

原帖[https://github.com/onmyway133/blog/issues/198](https://github.com/onmyway133/blog/issues/198)