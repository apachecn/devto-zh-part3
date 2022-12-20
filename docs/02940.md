# Grails 4 升级说明

> 原文：<https://dev.to/erichelgeson/grails-4-upgrade-notes-3cja>

Grails 4 是 Grails web 框架的下一个版本。Grails 是一个开发人员友好的框架，构建在 Spring-boot、Groovy 和现在的 Micronaut 之上。

# 状态

grails 4[发布](https://objectcomputing.com/news/2019/07/11/grails-4-ga-released)！

我已经将一个重要的应用程序从 Grails 3.3.9 移植到 4.0.0，并更新到 JDK11。整个工作只花了几个小时——这对许多升级者来说是个好消息。

初始应用程序启动时间大约为 15 秒——比 Grails 3.3.9 中的相同应用程序快 2 倍。

重装已经改变，有几个选项。默认选项是从`spring-dev-tools` -快速重启。这将比正常重启更快地重启你的应用，但是对于大型项目来说仍然非常慢。

热重装选项是[JRE bel](https://jrebel.com/software/jrebel/)——一个为 JDK8/11 工作的付费选项——或者是`spring-loaded`，尽管它只支持 JDK8 并且不再维护。[弹簧加载指令](https://github.com/grails/grails-core/pull/11441)

IDE 支持 Grails 4 中的所有工作(与 3.3.9 相同)——对于任何 Grails、Groovy 或 Java 开发人员来说，IntelliJ 仍然是我推荐的 IDE。

# 更新

Grails 4 及其构建框架有大量的更新。如果你要更新你的应用，以下是你应该查看的更新和发行说明列表。

*   Grails 4
    *   [升级说明](http://docs.grails.org/4.0.x/guide/upgrading.html)
    *   [新应用不同于 3.3.10 - > 4.0.0](https://github.com/erichelgeson/grails-versions/compare/3.3.10...4.0.0)
*   [Micronaut 1.1](https://micronaut.io)
*   [Spring Boot 2.1](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.1-Release-Notes)
*   [弹簧 5.1](https://github.com/spring-projects/spring-framework/wiki/Upgrading-to-Spring-Framework-5.x)
*   [Groovy 2.5](http://groovy-lang.org/releasenotes/groovy-2.5.html)
*   [Hibernate 5.3/5.4 支持](http://hibernate.org/orm/releases/5.4/)
*   [Java 11](https://www.oracle.com/technetwork/java/javase/11all-relnotes-5013287.html)

# 安装

使安装和管理一个 JVM 和许多框架变得轻而易举:

```
sdk install java 11.0.2-open
sdk install grails 4.0.3 
```

# 插件

插件是 Grails 开发人员体验的重要组成部分。幸运的是，许多在 Grails 3 中工作的插件可以在 Grails 4 中开箱即用——或者只需稍加修改。

## 工作插件

*   弹簧-安全-核心`4.0.0`

    *   新的委派密码策略:
    *   [https://www.baeldung.com/spring-security-5-password-storage](https://www.baeldung.com/spring-security-5-password-storage)
    *   如果您已经使用 bcrypt，也可以一次完成:
    *   `UPDATE my_users SET password = CONCAT('{bcrypt}', password);`
*   spring session-redis(启动启动器)

    *   更新的依赖关系

```
compile "org.springframework.boot:spring-boot-starter-data-redis"
compile "org.springframework.session:spring-session-data-redis" 
```

*   弹簧腹板插座

```
compile "org.grails.plugins:grails-spring-websocket:2.5.0.RC1" 
```

*   grails-石英

```
buildscript {
    dependencies {
        classpath 'org.grails.plugins:quartz:2.0.13' // Needed to compile *Job classes
    }
}

dependencies {
    compile 'org.grails.plugins:quartz:2.0.13'
    compile 'org.quartz-scheduler:quartz:2.2.1' // Is not pulled in by default
} 
```

*   数据库迁移插件`3.1.0.RC1`

*   资产管道`3.1.0`

*   外部配置

    *   作品-使用 RC1+

```
 compile 'org.grails.plugins:external-config:2.0.0' 
```

*   Spring 安全用户界面

    *   [https://github . com/grails-plugins/grails-spring-security-ui/issues/111](https://github.com/grails-plugins/grails-spring-security-ui/issues/111)
*   Schwartz 插件

    *   `2.0.0.RC1`
*   Grails 缓存插件

    *   `5.0.0.RC1`
*   Spring 安全 OAuth2 提供程序

    *   `4.0.0-RC1`
*   哨兵

*   Grails 邮件

    *   `3.0.0`

## 不工作

*   审计日志插件
    *   WIP @[https://github . com/robertoswald/grails-audit-logging-plugin/pull/183](https://github.com/robertoschwald/grails-audit-logging-plugin/pull/183)

## 已解决

*   ~~资产管道把手问题:~~
    *   需要更新以将`//= require handlebars/handlebars.js`更改为`//= require handlebars-runtime`

```
2019-01-13 16:59:42.280  WARN --- [nio-8080-exec-1] asset.pipeline.DirectiveProcessor        : Unable to Locate Asset: /handlebars/handlebars.js
Warning: Nashorn engine is planned to be removed from a future JDK release 
```

*   ~~Gson 视图~~
    *   已解决。
    *   无法使用简单对象呈现 json(只需获得`null`响应)
    *   域类不呈现:

```
 Model variable [someDomain] of with value [Description] type [com.x.SomeDomain] is not of the correct type [com.x.SomeDomain]. Stacktrace follows: 
```

*   未决问题[https://github.com/grails/grails-views/issues/202](https://github.com/grails/grails-views/issues/202)

## 其他注释注释

*   仅适用于 JDK8 的热重装

    *   `spring-loaded`最高仅支持 JDK8。
    *   快速重新加载需要大约 5-6 秒(希望可以改善)
*   `org.grails:grails-datastore-rest-client`不再保持

    *   使用不同的 http 客户端，例如
        *   `micronaut-http-client`、`http-builder-ng`、`feign`、`okhttp`等
    *   或者使用旧的无人维护的 GORM 6 版本:

```
compile('org.grails:grails-datastore-rest-client:6.1.9.RELEASE'){
    exclude group:'org.grails', module:'grails-plugin-converters'
} 
```

*   Groovy 2.5.x

    *   默认情况下不包括日期实用程序助手-需要添加它们。
    *   `compile 'org.codehaus.groovy:groovy-dateutil'`
    *   (应该刚刚更新到`java.time.*`)
    *   JDK 11 警告(预期)
    *   [https://dev . to/erichelgeson/remove-launch-reflective-access-warnings-in-grails-4-393 o](https://dev.to/erichelgeson/removing-illegal-reflective-access-warnings-in-grails-4-393o)
    *   `.pop()` &集合行为更改为与 Java 一致-[https://issues.apache.org/jira/browse/GROOVY-9388](https://issues.apache.org/jira/browse/GROOVY-9388)
*   依赖性变化(Gradle 5.1)

    *   一个问题是像`com.jameskleeh:excel-builder`这样的依赖关系不能拉入它们的依赖关系(例如:Apache POI)
*   休眠已弃用警告(预期)

```
Hibernate's legacy org.hibernate.Criteria API is deprecated; use the JPA javax.persistence.criteria.CriteriaQuery instead
// and
HHH020100: The Ehcache second-level cache provider for Hibernate is deprecated.  See https://hibernate.atlassian.net/browse/HHH-12441 for details. 
```

*   将它们藏在`logback.groovy`
    *   `logger('org.hibernate.orm.deprecation', ERROR, ['STDOUT'], false)`

# 自己的笔记或小贴士？

加入我们的 [grails 社区](https://grails-slack.cfapps.io/) slack 频道或在下面发表评论！