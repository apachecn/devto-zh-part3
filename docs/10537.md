# 给 Gradle 的组织建议

> 原文：<https://dev.to/johnson_cor/organizational-tip-for-gradle-523o>

如果你和我一样，格雷尔有时会有点神秘。我试图更好地理解它，以及我能做些什么来使它成为我自己的！

这是 TL。我在帖子中提到的博士:

1)在您的根项目文件夹中创建一个 libraries.gradle 文件

2)在项目级 gradle 中，在 buildscript 块上方编写 apply from:“libraries . gradle”

3)在 libraries.gradle 中声明您的依赖关系，使用`ext.versions`表示版本代码，`ext.libraries`表示 gradle 工件，`ext.libPacks`表示实现组

4)要在模块的 build.gradle 文件中的 dependencies 块中使用，您只需编写 libPack.retrofit 或 libPack.glide

再啰嗦一点的解释在这里:[https://medium . com/@ cor . Johnson/a-simple-way-to-improve-your-Android-build-grad le-files-c 7325731 B3 ea](https://medium.com/@cor.johnson/a-simple-way-to-improve-your-android-build-gradle-files-c7325731b3ea)

* * *

如果任何人有他们使用的任何其他令人敬畏的诡计，我很乐意听到他们！