# [Android]如何用 Mockito2 模拟最终类

> 原文：<https://dev.to/rkowase/android-how-to-mock-final-class-with-mockito2-4ei1>

在 Android 项目中，`data`类是`final`。所以`final`班不能延期。

## 一期

当使用`mock()`、`spy()`等来模仿`data`类时，就会出现这种错误...

```
org.mockito.exceptions.base.MockitoException: 
Cannot mock/spy class net.kikuchy.example.ComplexData
Mockito cannot mock/spy because :
 - final class 
```

## 解

添加`mockito-inline`

```
dependencies {
    testImplementation "org.mockito:mockito-core:$mockito_version"
    testImplementation "org.mockito:mockito-inline:$mockito_version"
} 
```