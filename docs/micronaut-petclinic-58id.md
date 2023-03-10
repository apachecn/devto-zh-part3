# Micronaut 宠物诊所

> 原文：<https://dev.to/bufferings/micronaut-petclinic-58id>

我尝试用 [Micronaut](https://docs.micronaut.io) 来实现 [Spring PetClinic](https://github.com/spring-projects/spring-petclinic) 而不是使用 Spring。

[https://github.com/bufferings/micronaut-petclinic](https://github.com/bufferings/micronaut-petclinic)

该应用程序使用百里香叶、JPA(Hibernate)和 DI。但是它在几百毫秒内就从本机映像开始了。好快啊！

在这张图片中，它开始于 324 毫秒:

[![Screen Capture](img/987b1fe0d49f6ed6fd91c5d8bf711d3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUDyo-OB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wtqucs1gzdghjbsyymya.png)

我还没有实现所有的功能，它仍然很脏，但是基本的功能可以工作。

## 如何尝试(非原生图像)

### 1。克隆项目

```
git clone https://github.com/bufferings/micronaut-petclinic.git
cd micronaut-petclinic 
```

Enter fullscreen mode Exit fullscreen mode

### 2。启动 PostgreSQL

这个 Petclinic 使用 PostgreSQL。您可以用 docker:
启动 PostgreSQL

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

### 3。运行应用程序

```
./mvnw compile exec:exec 
```

Enter fullscreen mode Exit fullscreen mode

### 3-2。或者您可以从 JAR 运行它

```
./mvnw package
java -jar target/micronaut-*.jar 
```

Enter fullscreen mode Exit fullscreen mode

### 4。访问宠物诊所

然后可以在这里访问 pet clinic:[http://localhost:8080/](http://localhost:8080/)

有了 JAR 文件，在我的笔记本电脑上启动这个应用大约需要 5 秒钟。

## 如何尝试原生映像

Micronaut 支持 GraalVM 本机映像。所以我让这个宠物诊所作为本地映像运行。

```
./mvnw package && docker build -t micronaut-petclinic . 
```

Enter fullscreen mode Exit fullscreen mode

我通常用☕(在我的笔记本电脑上大约 10 分钟)来等待构建完成。

```
# Docker for Mac or Windows
export HOST_NAME=host.docker.internal
# Linux
export HOST_NAME=172.17.0.1

docker run --rm -p 8080:8080 -e JDBC_URL=jdbc:postgresql://${HOST_NAME}:5432/petclinic micronaut-petclinic 
```

Enter fullscreen mode Exit fullscreen mode

然后大约需要 300 毫秒才能启动。(๑•̀ㅂ•́)و✧