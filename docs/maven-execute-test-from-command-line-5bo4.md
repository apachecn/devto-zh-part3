# Maven:从命令行执行测试

> 原文：<https://dev.to/adzubla/maven-execute-test-from-command-line-5bo4>

使用 maven 执行一个类的 JUnit 测试:

```
mvn test -Dtest=my.fully.qualified.ClassName#testOne+testTwo 
```