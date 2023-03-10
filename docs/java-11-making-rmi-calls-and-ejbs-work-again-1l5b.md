# Java 11:让 RMI 调用和 EJB 再次工作

> 原文：<https://dev.to/dbh/java-11-making-rmi-calls-and-ejbs-work-again-1l5b>

作为一个新项目的一部分，我探索了重复使用 EJB 的可能性。这是为了利用现有的业务逻辑，而不是克隆，克隆也需要单独维护。

尝试用 Java 11 编译 EJB 调用代码提供了一个快速提示。作为 JEP 320 的一部分，Java 中删除了以下内容。

*   java.corba 模块
*   javax.rmi.CORBA
*   javax . RMI(RMI IIOP 软件包)

无法编译的示例代码段

```
Context context = new InitialContext();
Object raw = context.lookup(jndiName);
ARemoteType type = (ARemoteType )PortableRemoteObject.narrow(raw, ARemoteType .class); 
```

幸运的是，在 Eclipse Foundation 的 Java EE 下，CORBA 和 RMI-IIOP 的 Glassfish 实现是可用的！包括下面的 Gradle 片段允许代码编译和工作，无需任何更改。

```
implementation group: 'org.glassfish.corba', name: 'glassfish-corba-orb', version: '4.2.0' 
```

*参考文献*

*   [我博客上的同一篇短文](https://www.davidbharrison.com/java-11-rmi-corba-and-ejbs/)
*   [JEP 320:移除 Java EE 和 CORBA 模块](http://openjdk.java.net/jeps/320#CORBA-and-JTA-modules)
*   [GlassFish CORBA](https://javaee.github.io/glassfish-corba/)
*   [Maven 知识库](https://mvnrepository.com/artifact/org.glassfish.corba/glassfish-corba-orb)