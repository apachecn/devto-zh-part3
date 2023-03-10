# 用 Spring Boot 和 Vue.js 创建一个单页应用程序

> 原文：<https://dev.to/tunaranch/create-a-single-page-app-with-spring-boot-and-vue-js-2on>

# 目标

用 [Spring Boot](https://spring.io/projects/spring-boot) 和 [Vue.js](https://vuejs.org/v2/guide/) 创建一个单页应用:

*   一个 maven 版本用于前端和后端。
*   前端捆绑到引导应用程序中。
*   在路由器历史模式下使用 Vue 路由器，这样我们就没有了地址栏里的`#`。

# 先决条件

您需要安装:

*   npm(在 macOS 上，您可以简单地`brew install npm`)
*   [vista-CLI](https://cli.vuejs.org/guide/#components-of-the-system)(`npm install -g @vue/cli`
*   [JDK](https://jdk.java.net/11/) (这个例子使用 java 11，但是任何版本都可以，只需在创建 spring 项目时更改 java 版本)
*   [httpie](https://httpie.org) (可选。你可以使用 [https://start.spring.io](https://start.spring.io) 来引导你的 spring 项目)。

# 循序渐进

## 创建 Spring Boot 项目

从终端

```
$ http https://start.spring.io/starter.tgz \
 artifactId==cafe \
 javaVersion==11 \
 language==kotlin \
 name==Cafe \
 dependencies==webflux,devtools,actuator \
 baseDir==cafe | tar -xzvf - 
```

这将在`cafe/`下给你一个基本的 spring boot 项目。

测试构建以确保其工作正常:

```
$ ./mvnw test
[INFO] Scanning for projects...
[INFO] 
[INFO] --------------------------< com.example:cafe >--------------------------
[INFO] Building Cafe 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
...
[INFO] 
[INFO] Results:
[INFO] 
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  13.718 s
[INFO] Finished at: 2019-03-30T12:19:24+10:00
[INFO] ------------------------------------------------------------------------ 
```

## 创建 Vue 项目

使用 vue-cli 生成 Hello World Vue CLI 项目。

```
$ cd src/main
$ vue create frontend \
    --no-git -i '{
  "useConfigFiles": false,
  "plugins": {
    "@vue/cli-plugin-babel": {},
    "@vue/cli-plugin-typescript": {
      "classComponent": true,
      "useTsWithBabel": true
    },
    "@vue/cli-plugin-eslint": {
      "config": "standard",
      "lintOn": [
        "save"
      ]
    }
  },
  "router": true,
  "routerHistoryMode": true,
  "cssPreprocessor": "node-sass"
}' 
```

## 配置 javascript 构建输出目录

配置 webpack，使编译后的静态内容位于`target`之下，与 maven 约定保持一致。Spring Boot 在类路径根提供来自`public`的静态资源，所以我们也会考虑到这一点。

编辑`src/main/frontend/vue.config.js`:

```
module.exports  =  {  outputDir:  '../../../target/frontend/public'  } 
```

## 配置 maven build 编译 Vue 项目

我们需要确保构建的静态资源最终位于正确的位置，这样 maven build 和 spring 就知道了

### 配置 npm 构建

将此插件添加到您的`pom.xml`的`plugins`部分:

```
<project>
  ...
  <build>
    <plugins>
      ...
      <plugin>
        <groupId>com.github.eirslett</groupId>
        <artifactId>frontend-maven-plugin</artifactId>
        <version>1.7.5</version>
        <executions>
          <execution>
            <id>install node and npm</id>
            <goals>
              <goal>install-node-and-npm</goal>
            </goals>
            <configuration>
              <nodeVersion>v11.12.0</nodeVersion>
            </configuration>
          </execution>
          <execution>
            <id>npm install</id>
            <goals>
              <goal>npm</goal>
            </goals>
            <configuration>
              <arguments>install</arguments>
            </configuration>
          </execution>
          <execution>
            <id>npm build</id>
            <goals>
              <goal>npm</goal>
            </goals>
            <phase>generate-resources</phase>
            <configuration>
              <arguments>run build</arguments>
            </configuration>
          </execution>
        </executions>
        <configuration>
          <workingDirectory>${project.basedir}/src/main/frontend</workingDirectory>
          <installDirectory>${project.build.directory}/node</installDirectory>
        </configuration>
      </plugin>
      ...
    <plugins>
  </build>
</project> 
```

通过运行`./mvnw process-resources`进行测试。您应该在`target/frontend/`中看到 npm 构建的输出。

### 将编译后的静态资源添加到 maven build 中

通过向您的`pom.xml`添加一个`resources`部分，将生成的静态组件作为资源添加到您的构建中。

```
 <project>
  ...
  <build>
    ...
    <resources>
      <resource>
        <directory>${project.build.directory}/frontend</directory>
      </resource>
    </resources>
    ...
  </build>
</project> 
```

### 配置 spring boot 插件以包含静态资源

将这个额外的`configuration`元素添加到`spring-boot-maven-plugin`的配置中，这样它将被视为 Spring Boot 应用程序的一部分。

```
<project>
  ...
  <build>
    <plugins>
      ...
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <folders>
            <folder>${project.build.directory}/frontend</folder>
          </folders>
        </configuration>
      </plugin>
      ...
    <plugins>
  </build>
</project> 
```

快到了！如果你运行`./mvnw spring-boot:run`并将浏览器指向[http://localhost:8080/index . html](http://localhost:8080/index.html)，你应该会看到 vue hello world 页面的一半。我们需要在后端做更多的工作，让事情正常进行。

## 重写 URL 以使路由器历史模式工作

创建一个过滤器，将所有不是预设路径的内容路由到静态索引页面。

我们将让 boot 处理以下路径:

*   Spring Boot 的致动器具有用于健康检查、度量等的端点
*   `/api`:这个 app 的后台 API 可以走这个路径
*   `/js`、`/css`、`/img`:静态资源

```
package com.example.cafe.web

import org.springframework.stereotype.Component
import org.springframework.web.server.ServerWebExchange
import org.springframework.web.server.WebFilter
import org.springframework.web.server.WebFilterChain
import reactor.core.publisher.Mono

@Component
class VueRoutePathFilter : WebFilter {

    companion object {
        val BOOT_PATHS = listOf(
            "/actuator/",
            "/api/",
            "/js/",
            "/css/",
            "/img/"
        )

        const val SPA_PATH = "/index.html"
    }

    override fun filter(exchange: ServerWebExchange,
                        chain: WebFilterChain): Mono<Void> {
        if (isApiPath(exchange.request.uri.path)) {
            return chain.filter(exchange)
        }

        return chain
            .filter(exchange
                .mutate()
                .request(exchange.request
                    .mutate().path(SPA_PATH)
                    .build())
                .build())
    }

    private fun isApiPath(path: String): Boolean {
        return BOOT_PATHS.any { path.startsWith(it) }
    }
} 
```

您现在应该能够点击 [http://localhost:8080](http://localhost:8080) 来获得 vue Hello World 页面。

这个项目的[样本代码](https://github.com/tunaranch/spring-boot-vue-spa/tree/create-hello-world-spa)在 GitHub 上。尽情享受吧！