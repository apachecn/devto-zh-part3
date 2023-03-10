# 通过 Android 的通用共享首选项获得灵活性

> 原文：<https://dev.to/alialp/gain-flexibility-with-generic-shared-preferences-for-android-55o>

如果你已经登陆这里，你已经体验到了保持 Android 的[共享偏好](https://developer.android.com/reference/android/content/SharedPreferences)在线的艰辛。本文将展示一种实用的方法，将共享偏好的所有可能的读写方法抽象成一个简单的泛型类。

## 背景

基本上，共享首选项是一个 XML 文件，它充当用户首选项(设置)的小型存储装置。在开发 Android 应用程序时，有大量的功能或设置与用户的口味紧密相关，如颜色、主题、字体、铃声等，因此这些偏好需要保存在某个地方，以便在下次执行应用程序时检索，这时就需要一个小的存储空间。Android 通过引入共享偏好库，提供了一种简单的机制来实现用户偏好的存储。

背景颜色等设置的简单存储是这样的

```
SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(applicationContext);
SharedPreferences.Editor editor = sp.edit();
editor.putString("background_color", "green");
editor.apply(); 
```

Enter fullscreen mode Exit fullscreen mode

然后 Android 会像这样保存在/data/data/YOUR _ APP _ PACKAGE _ NAME/shared _ prefs/YOUR _ APP _ PACKAGE _ NAME _ preferences . XML 中

```
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
    <string name="background_color">green</string>
</map> 
```

Enter fullscreen mode Exit fullscreen mode

稍后“背景颜色”的值可以从共享的首选项中检索，就像这样

```
SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(applicationContext);
String defaultValue="white";
String Value = sp.getString("background_color", defaultValue);
Issue 
```

Enter fullscreen mode Exit fullscreen mode

从上面可以看出，在共享首选项中存储字符串值时，需要调用 putString 方法和 getString 方法来检索它。看起来非常简单，直到应用程序开始增长，并且需要存储的首选项数量也随之增长。

另一方面，为了存储 Boolean，需要在 order 中调用 putBooean()/getBoolean()来分别存储和检索首选项。

## 解

将出现的第一个解决方案是使用一个静态助手类来处理这个操作，因此，为了从共享首选项中检索字符串值或将字符串值存储到共享首选项中，将需要类似下面的代码

```
private static String getPrefString(Context context,String prefName,String defaultValue){
    SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(context);
    String ret = sp.getString(prefName, defaultValue);
    return ret;
}

private static void setPrefString(Context context,String prefName,String value){
    SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(context);
    SharedPreferences.Editor editor = sp.edit();
    editor.putString(prefName, value);
    editor.apply();
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，对于 Boolean、StringSet、Float、Long 和 Integer，为了能够处理所有可能类型的存储/检索，也需要两种方法(总共 12 种方法),人们可以称之为混乱和不必要的。

另一个解决方案是使用泛型，如下面的代码