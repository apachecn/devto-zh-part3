# 我如何使我的遗留应用模块化-第 1 部分

> 原文：<https://dev.to/dbottillo/how-i-have-modularized-my-legacy-app---part1-4mde>

我们都去过那里。无论是团队越来越大，还是你的应用程序越来越老，都有一个时间点来扩大你的 Android 应用程序的开发，你开始考虑如何模块化代码库。

当你达到这一点时，事情开始变得混乱，你可能会开始考虑放弃 Gradle 而支持 Bazel，或者你可能会考虑应用关于如何创建模块化应用程序的许多建议中的一个。但是应用捆绑包呢？还有即时 app？甚至可能是动态特征？匕首谁？更重要的是，适合你自己的应用和团队的策略怎么样？真正的问题是，你如何从一个单一的应用程序转移到任何可以纵向扩展并拥有多个模块的应用程序？

你不可能一夜之间就做到这一点，即使对于一个小的应用程序也是如此，因为有些东西会被破坏和忽略，所以我建议采取一种更加循序渐进的方法。

我不得不为我日常工作中的大型应用处理这个过程，我决定首先模块化我自己的一个小型开源应用。这是一个应用程序，用来浏览一个名为魔术聚会([https://github.com/dbottillo/MTGCardsInfo](https://github.com/dbottillo/MTGCardsInfo))的可收藏纸牌游戏的纸牌。该应用程序相当小:不到 10 个活动，大量的逻辑围绕着 1k 日活跃用户的数据库，所以我认为这可能是一个学习这个过程的好用例。

那么从哪里入手呢？首先我们应该考虑我们想要的模块的结构，我认为最简单的结构是拥有三个模块，分别叫做`app`、`legacy`和`core`:

*   `app`是生成项目的`APK`的顶级模块，其理念是应用程序知道下面的一切，理想情况下应该只包含最初可以存在于任何其他模块中的`application`类
*   是一个库模块，包含了所有需要在模块间共享的东西，例如 api 模型，util 类等等..。
*   `legacy`是你当前的代码库，它位于`app`之下和`core`之上，原因是理想情况下你的`legacy`模块将被转换成许多有`core`在下面的兄弟模块，并将你的特性提供给你的`app`模块

[![module structure](img/0f0353e9210385fa159818901cc9be28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lBOlkeXx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cffzuh0xwjczb6c7j3y4.jpg)

这种方法还有其他选择，比如你可以考虑在`legacy`之上创建`core`并从中提取代码，但我不认为这是一个好主意:如果`core`在`legacy`之上，那么如果你将一个类从`legacy`移动到`core`，那么它的依赖关系仍然是可用的(记住每个模块仍然可以从下面的模块中访问任何东西)，所以它不会真正帮助你解开任何依赖关系。

开始时，我建议将所有的模块都做成`android`模块，这样更容易移植(将来你可以考虑从它们中移除`android`依赖)。我还建议创建一个`config-android.gradle`来导入其他模块以共享 android 配置，设置应该是这样的:

```
app/src/main/AndroidManifest.xml
app/build.gradle
app/src/main/java/… → empty
legacy/src/main/AndroidManifest.xml → existing manifest
legacy/build.gradle → existing gradle file
legacy/src/main/java/… → all your existing codebase
core/src/main/AndroidManifest.xml
core/build.gradle
core/src/main/java/… → empty
build.gradle → generic configurations across modules
settings.gradle → project configuration
config-android.gradle → shared android configuration across modules 
```

让我们来看看每个文件的详细信息。

```
File: app/src/main/AndroidManifest.xml

<manifest package="com.dbottillo.mtgsearchfree.app" 
   xmlns:android="http://schemas.android.com/apk/res/android" 
   xmlns:tools="http://schemas.android.com/tools">
    <application        
           android:name="com.dbottillo.mtgsearchfree.MTGApp"
           android:icon="@drawable/ic_launcher"                   
           android:label="@string/app_name"         
           android:theme="@style/MTGSearchTheme">
    </application>
</manifest> 
```

这里没什么特别的，清单基本上是空的，它只是引用了目前存在于`legacy`模块中的应用程序类。将应用程序移动到这里的一个结果是，它还需要将图标`@drawable/ic_launcher`移动到`app` src 目录。

```
File: app/src/build.gradle

apply plugin: 'com.android.application'
apply from: '../config-android.gradle'
android {    
   defaultConfig {        
      applicationId 'com.dbottillo.mtgsearchfree'        
   }
   signingConfigs {       
      debugConfig { ... }
      releaseConfig { ... }
   }
   buildTypes{
       debug {          
           applicationIdSuffix ".debug"            
           versionNameSuffix "_dev"            
           signingConfig signingConfigs.debugConfig            
           debuggable true
       }
       release {            
           debuggable false            
           signingConfig signingConfigs.releaseConfig        
       }
   }
}
dependencies {    
    implementation project(':MTGSearch')
    // dependencies
} 
```

`apply from`让你导入另一个`.gradle`文件的内容，我稍后会详细介绍，现在你可以看到一些关于 android 的信息丢失了(最小 sdk，目标 sdk，等等..).那是因为它们是在另一个 gradle 文件中定义的，这个文件只指定与`app`模块相关的东西。请注意，您需要使用该模块来创建 APK 或在设备上运行应用程序。

```
File: core/src/main/AndroidManifest.xml

<manifest    
    package="com.dbottillo.mtgsearchfree.core"     
    xmlns:android="http://schemas.android.com/apk/res/android">
</manifest> 
```

核心的清单是空的，这在这个阶段是有意义的，因为模块是空的，因此没有活动、服务等..在本模块中定义。

```
File: core/build.gradle
apply plugin: 'com.android.library'
apply from: '../config-android.gradle'
dependencies{    
    // dependencies
} 
```

作为清单，即使是 gradle 文件也非常小:它被定义为一个`com.android.library`并继承了 android 配置。

```
File: build.gradle
buildscript {
    repositories {        
         google()        
         mavenCentral()        
         maven { url "https://maven.google.com" } 
         jcenter()    
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.2.1'        
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.3"
    }
}
allprojects {    
    repositories {        
         google()        
         mavenCentral()        
         jcenter()        
         maven { url "https://maven.google.com" }
     }
     dependencies {
         // your dependencies
     }
}
File: settings.gradle
include ':legacy', ':core', ':app' 
```

在根目录`build.gradle`中，你可以为你的其他模块定义所有的库和依赖关系，而在`settings.gradle`中，所有的模块都是可用的，这是一个非常简单的分级配置。
更有趣的是`config-android.gradle` :

```
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
apply plugin: 'kotlin-kapt'
android {    
    compileSdkVersion 28    
    buildToolsVersion '28.0.3'
    defaultConfig {       
          minSdkVersion 21        
          targetSdkVersion 28        
          versionCode 101        
          versionName "3.6.0"    
    }
    buildTypes {        
          release {}        
          debug {}   
    }
}
androidExtensions {    
     experimental = true
}
kapt {    
     useBuildCache = true
} 
```

正如我前面提到的，这个 gradle 文件是跨模块共享的，所以它定义了跨模块的所有属性，这是处理更改的一种更简单的方式:否则，例如，每次你想修改版本代码时，你都需要在每个模块中这样做。

对于`legacy`模块来说，`build.gradle`和`AndroidManifest.xml`基本相同:`legacy/build.gradle`文件需要导入`config-android.gradle`，并将插件从应用程序更改为库。还需要从 AndroidManifest.xml 中删除 application 类的定义，因为它没有在`app`模块中定义。

我认为这是从一个模块移动到三个模块所需的最小量！接下来的步骤是将应用程序类移动到`app`模块，然后在将类从`legacy`移动到`core`时理清依赖关系。我将在接下来的部分中讲述这些步骤:)

如果你想检查真实的代码，你可以看看我在开始提到的应用程序的提交:[https://github . com/dbottillo/MTGCardsInfo/commit/6 EC 4a 1 BD 72d 79 Fe 13 f 0 b 7 c 602 a 56 ECA 32 A8 c 43 e](https://github.com/dbottillo/MTGCardsInfo/commit/6ec4a1bd72d79fe13f0b7c602a56aeca32a8c43e)
记住，`legacy`实际上被称为`MTGSearch`，因为这个项目实际上是一个真实的项目，所以每个文件的复杂性比这里描述的稍微大一些。