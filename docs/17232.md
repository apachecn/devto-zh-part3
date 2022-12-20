# JBOSS EAP 7.1+Spring boot hello world & visual VM 观察

> 原文：<https://dev.to/peysal/jboss-eap-71--spring-boot-hello-world--visualvm-observation-2e1i>

# 简介

在我之前的帖子中，我们已经知道 jboss-eap-7.1 在默认情况下(没有部署)会消耗你多少内存。现在来看看 spring boot hello world 的简单部署将如何消耗 JBOSS 中的内存。

## 涉及的工具

1.  jboss eap 7.1(咄)
2.  visualVm

## 我们是如何做到的

1.  创建一个简单的 hello world spring mvc
2.  将它部署到已经运行的 jboss eap 中
3.  监控虚拟虚拟机的内存使用增加情况

### 输出

部署后的堆大小:
[![heap size after deployment](img/3feedc81511ef65bb8df30a3c2bc4fb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CCuFYR7M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lsm7z9hso5wk6og0jchp.png)

部署后的元空间:
[![metaspace after deployment](img/de6dcf99e7305b8bd6624ad43a4734fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M-jnolzV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nu6igcauhqjpps1mw3y0.png)

类加载后部署:
[![class loaded after deployment](img/ab0272014b87c67c4ca36e7eb898383f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rcVSQGt_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ev1d9r7wd859mqzxn35t.png)

### 对刚刚发生的事情有些理论？

1.  嘿，这场战争有一堆库和类需要加载
2.  对象创建导致堆大小增加

### 事情要深思

1.  如果我们在同一个 jboss 实例中部署另一个 war 会怎么样？
2.  如果这场新战争与之前的战争共享同一个库会怎么样？