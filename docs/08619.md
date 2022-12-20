# Maven:从命令行执行 main 方法

> 原文：<https://dev.to/adzubla/maven-execute-main-method-18nh>

使用 maven 执行一个类的`main`方法:

```
mvn exec:java -Dexec.mainClass="com.example.Main" [-Dexec.args="argument1"] ... 
```