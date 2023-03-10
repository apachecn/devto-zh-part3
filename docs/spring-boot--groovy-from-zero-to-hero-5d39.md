# Spring Boot + Groovy:从零到英雄

> 原文：<https://dev.to/jorge_rockr/spring-boot--groovy-from-zero-to-hero-5d39>

Java 的我不知道是猫头鹰吗我不知道

[![](img/6bcf860c65933b5ad43ade059362050c.png)](https://i.giphy.com/media/54PaD9dWT0go/giphy.gif)

我不喜欢使用 Java(也请不要使用它)，但有时我不得不这样做...事实就是如此。

[![](img/457de64248b94695be12608255358ff9.png)](https://i.giphy.com/media/eBCnpuRGBhQGY/giphy-downsized-large.gif)

几周前，我在 [Indigo](https://www.dsindigo.com/) 开始了一个新项目(请跟随他们！)选择的框架是 [Spring Boot](http://spring.io/projects/spring-boot) ，这一次我没有选择，因为和我一起开发的另一个人喜欢组合 **Java + Spring Boot** 。

[![](img/bfdc2bf41f4be66e9182762a8f026e8b.png)](https://i.giphy.com/media/Pch8FiF08bc1G/giphy.gif)

但是为什么我说“结合”？🤔嗯，有一些在 JVM 上工作的编程语言，比如 Kotlin、Scala 和我最喜欢的 Groovy！。

## 什么是 Groovy？

根据它的官方页面:

> Apache Groovy 是一种强大的、可选类型的动态语言，具有静态类型和静态编译能力，用于 Java 平台，由于其简洁、熟悉且易于学习的语法，旨在提高开发人员的生产率。它可以与任何 Java 程序顺利集成，并立即为您的应用程序提供强大的功能，包括脚本功能、特定领域的语言创作、运行时和编译时元编程以及函数式编程。

换句话说，你可以做更好的东西，写更少的代码，获得一些 Java 做不到的很酷的特性。

## 我们的榜样会是什么？

这次我们将创建一个口袋妖怪 API！！让我们抓住他们

[![](img/546b94c1b603da43c38267ee0e2e5103.png)](https://i.giphy.com/media/W04QVzelTHsNW/giphy.gif)

我们的 API 必须:

*   获取所有培训师的信息
*   获取特定教练的信息
*   获得所有特定训练师的口袋妖怪

## 那会用什么来解决呢？

*   Java 8
*   Gradle
*   Spring Boot
*   绝妙的
*   关系型数据库
*   飞行路线数据库

## 我需要安装什么？

*   Java 8 或更高版本
*   Gradle
*   关系型数据库

强烈推荐你通过 [SDKMAN](https://sdkman.io) 安装 Java 和 Gradle。

我们开始吧！

## 1。用 Gradle 创建基础项目

创建一个目录，然后创建一个名为`build.gradle`的文件。该文件将:

*   下载所有依赖项
*   编译整个项目
*   运行 Spring Boot 服务器
*   如果需要，运行测试

所以，让我们加上下面几行:

```
buildscript {
  ext {
    springBootVersion = '2.0.6.RELEASE'
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
  }
}

apply plugin: 'groovy'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group = 'com.pokemon'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
  mavenCentral()
}

dependencies {
  implementation("org.springframework.boot:spring-boot-starter-web:${springBootVersion}")
  implementation('org.codehaus.groovy:groovy:2.5.3')
} 
```

这几行告诉 Gradle 将 Maven Central 配置为默认存储库，下载并使用`spring-boot-gradle-plugin`并下载以下依赖项:

*   **Spring Boot 网**:公开 REST 服务
*   **Groovy** :编译和解释所有 Groovy/Java 代码

我们的基础包名也将是`com.pokemon`,所以每个有这个包名的类都将由 Gradle 编译，由 Spring Boot 使用。

## 2。创建基本代码并首次运行它

在根目录下创建目录`src/main/groovy/com/pokemon`，然后创建一个名为`Application.groovy` :
的文件

```
package com.pokemon

import org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication

// In groovy every class is public by default
// so you don't need to write "public class" anymore
@SpringBootApplication
class Application {

    // Every method is public as well
    static void main(String[] args) {

        // You can omit the last method call parenthesis
        // This is the same as .run(Application, args)
        // also you can omit ;
        SpringApplication.run Application, args
    }
} 
```

打开您的终端，转到您的项目目录，键入命令`gradle bootRun`让您的应用程序像这样运行:

[![](img/2f0ac06ab14af3f32010e6fb0329564b.png)](https://i.giphy.com/media/8gKOQx8DDF6BkRRNhL/giphy.gif)

## 3。带有 Flyway 数据库的 SQL 表

使用 SQL 数据库通常是一件痛苦的事情。

你如何与你的团队分享数据库结构？

处理它的一种方法是创建一个 SQL 脚本，并与所有开发人员共享。

听起来不错，对吧？

[![](img/cbb3c1dd31e45e122f7c7464d6c9a9bd.png)](https://i.giphy.com/media/111ebonMs90YLu/giphy.gif)

但是，当您需要向脚本添加更改(比如修改现有的表或添加新表)时，您现在该怎么做呢？开发团队如何从这些变更中得到通知？

PHP Laravel 等框架引入了*迁移*的概念。对数据库的每次更改(创建表、添加字段、更改字段类型等)都是一次*迁移*。迁移按一定的顺序执行，并使数据库始终保持最新。

**Flyway DB** 将帮助我们解决问题，但在 Java (Groovy)中。

根据它的官方页面:

> 数据库的版本控制。跨您所有环境的强大模式演变。
> 轻松、愉快、简单的 SQL。

所以现在让我们将 **Flyway DB** 、 **Spring Data** 和 **MySQL Connector** 添加到我们的项目中。

编辑`build.gradle`文件和以下依赖项:

```
implementation('org.flywaydb:flyway-core:5.2.0')
implementation("org.springframework.boot:spring-boot-starter-data-jpa:${springBootVersion}")
implementation('mysql:mysql-connector-java:8.0.13') 
```

现在在`src/main/resources`目录下创建一个名为`application.properties` :
的新文件

```
spring.flyway.url=jdbc:mysql://localhost/
spring.flyway.user=your-username
spring.flyway.password=your-password
spring.flyway.schemas=your-database-name

spring.datasource.url=jdbc:mysql://localhost/your-database-name
spring.datasource.username=your-username
spring.datasource.password=your-password
spring.datasource.driver-class-name=com.mysql.jdbc.Driver 
```

不要忘记更改**你的用户名**、**你的密码**和**你的数据库名**的值。

在我的例子中，数据库名是`pokemon_example`。

**spring.flyway** 凭证让 **FlywayDB** 连接到数据库，并在每次运行项目时安装最新的更改。

**spring.datasource** 凭证让 **Spring Data** 连接到数据库并运行查询。

为了创建初始表，在`src/main/resources/db/migration`目录下创建一个名为`V1__creating_initial_tables.sql` :
的新文件

```
CREATE TABLE trainers(
  id INTEGER AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  level SMALLINT NOT NULL DEFAULT 1,
  PRIMARY KEY (id)
);

CREATE TABLE pokemon(
  id INTEGER AUTO_INCREMENT,
  name VARCHAR(20) NOT NULL,
  number SMALLINT NOT NULL,
  PRIMARY KEY (id)
);

CREATE TABLE wild_pokemon(
  id INTEGER AUTO_INCREMENT,
  combat_power SMALLINT NOT NULL DEFAULT 0,
  pokemon_id INTEGER NOT NULL,
  trainer_id INTEGER,
  PRIMARY KEY (id),
  FOREIGN KEY (pokemon_id) REFERENCES pokemon (id)
    ON DELETE CASCADE,
  FOREIGN KEY (trainer_id) REFERENCES trainers (id)
    ON DELETE SET NULL
); 
```

**Flyway DB** 使用模式 **VN__any_name.sql** 搜索`db/migration`目录中的所有文件，其中 N 是唯一版本，例如:

*   one
*   001
*   Five point two
*   1.2.3.4.5.6.7.8.9
*   Two hundred and five point six eight
*   20130115113556
*   2013.1.15.11.35.56
*   2013.01.15.11.35.56

我个人更喜欢使用这种版本控制:

*   V1 _ _ 无论如何. sql
*   V2 _ _ 无论如何. sql
*   等等。

最后，用`gradle bootRun`命令运行项目，然后查看数据库。

[![](img/359b3a466be818e94b483eb7b68ec697.png)](https://i.giphy.com/media/etn4pXJcDkR4DDYHiy/giphy.gif)

## 4。实体类(域)

回到代码，成功创建数据库后，是时候将这些表映射到类了。

在`src/main/groovy/com/pokemon`目录下，新建一个名为`entity`的目录，并新建 3 个文件:

*   `Trainer.groovy`
*   `Pokemon.groovy`
*   `WildPokemon.groovy`

*Trainer.groovy*

```
package com.pokemon.entity

import javax.persistence.Entity
import javax.persistence.Id
import javax.persistence.GeneratedValue
import javax.persistence.Table
import javax.persistence.GenerationType
import javax.validation.constraints.NotNull
import javax.persistence.Column

@Entity
@Table(name = "trainers")
class Trainer {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  Integer id

  @NotNull
  @Column(nullable = false)
  String name

  @NotNull
  @Column(nullable = false)
  Short level
} 
```

*Pokemon.groovy*

```
package com.pokemon.entity

import javax.persistence.Entity
import javax.persistence.Id
import javax.persistence.GeneratedValue
import javax.persistence.Table
import javax.persistence.GenerationType
import javax.validation.constraints.NotNull
import javax.persistence.Column

@Entity
@Table(name = "pokemon")
class Pokemon {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  Integer id

  @NotNull
  @Column(nullable = false)
  String name

  @NotNull
  @Column(nullable = false)
  Short number
} 
```

*WildPokemon.groovy*

```
package com.pokemon.entity

import javax.persistence.Entity
import javax.persistence.Id
import javax.persistence.GeneratedValue
import javax.persistence.Table
import javax.persistence.GenerationType
import javax.validation.constraints.NotNull
import javax.persistence.Column
import javax.persistence.ManyToOne
import javax.persistence.JoinColumn

@Entity
@Table(name = "wild_pokemon")
class WildPokemon {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  Integer id

  @NotNull
  @Column(name = "combat_power", nullable = false)
  Integer combatPower

  @ManyToOne
  @JoinColumn(name = "pokemon_id", referencedColumnName = "id", nullable = false)
  Pokemon pokemon

  @ManyToOne
  @JoinColumn(name = "trainer_id", referencedColumnName = "id", nullable = true)
  Trainer trainer
} 
```

在继续之前，请记住！！因为我们使用的是 Groovy，所以没有必要添加无聊的*getter*和*setter*:

```
// Groovy version
class Example {

  String property
}

Example ex = new Example(property: "hello world")
ex.property = "hola inmundo"

// -----------------------------------------------------

// Java version
public class Example {

  private String property;

  public Example(String property) {
    this.property = property;
  }

  public String getProperty() {
    return property;
  }

  public void setProperty(String property) {
    this.property = property;
  }
}

Example ex = new Example("hello world")
ex.setProperty("hola inmundo") 
```

少写代码 FTW！🤙

如您所见，基于表创建实体类很容易。在`WildPokemon`案例中，我们声明了两个多对一关系，一个到`Pokemon`类(或表),另一个到`Trainer`类(或表)。只有`Trainer`关系可以为空。

## 5。存储库类(持久层)

类是定义我们的查询的地方。它既简单又神奇。

记住我们的 API 目标:

*   获取所有培训师的信息
*   获取特定教练的信息
*   获得所有特定训练师的口袋妖怪

好了，让我们把这些特性翻译成 SQL 查询:

```
-- Get the information of all trainers
SELECT * FROM trainers;

-- Get the information of a specific trainer
SELECT * FROM trainers WHERE id = ?;

-- Get all caught pokemon of a specific trainer
SELECT * FROM wild_pokemon WHERE trainer_id = ?; 
```

似乎是合法的。

但是如果查询是用人类可读的文本编写的呢？

```
# Get the information of all trainers
find all trainers

# Get the information of a specific trainer
find trainer by id

# Get all caught pokemon of a specific trainer
find wild pokemon by trainer id 
```

信不信由你，使用来自 **Spring 数据**的`JpaRepository`接口，我们的查询是:

*   `findAll`
*   `findById`
*   `findByTrainerId`

非常容易！！🎉🎉🎉🎉🎉

让我们对它们进行编码，在`src/main/groovy/com/pokemon`目录下创建一个名为`repository`的新文件，并创建 2 个新文件:

*   `TrainerRepository.groovy`
*   `WildPokemonRepository.groovy`

*trainerrepository . groovy*

```
package com.pokemon.repository

import com.pokemon.entity.Trainer
import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface TrainerRepository extends JpaRepository<Trainer, Integer> {

  List<Trainer> findAll()

  Trainer findById(Integer id)
} 
```

*wild pokemon repository . groovy*

```
package com.pokemon.repository

import com.pokemon.entity.WildPokemon
import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface WildPokemonRepository extends JpaRepository<WildPokemon, Integer> {

  List<WildPokemon> findByTrainerId(Integer trainerId)
} 
```

就这么简单， **Spring Data** 将搜索所有用`@Repository`标注的类，并验证方法签名。

## 6。服务类别(业务层)

我们的 API 根本不需要业务逻辑，但创建服务类并在其中使用存储库仍然是一个好主意。

在`src/main/groovy/com/pokemon`目录中创建一个名为`service`的新目录，然后在`service`中创建一个名为`impl`的新目录。

我们的`service`目录将包含接口和`impl`它们的实现。

等等，什么？

[![](img/bb855f73648554790c7da7f11d802f06.png)](https://i.giphy.com/media/oOxBQwNqGwxeWLDF6A/giphy.gif)

它有它的解释。

假设你要实现一个存储文件的类:

```
class FileStorage {

  String saveFile(String base64) {
    // storing file in disk...
  }
} 
```

非常简单，但是你的客户端也需要将文件存储在亚马逊 S3 和 Azure Blob 中。解决问题最简单的方法是什么？

你可以创建一个接口，因为盘内存储、亚马逊 S3 存储和 Azure Blob 存储做的是同样的事情，保存一个文件:

```
interface Storage {

  String saveFile(String base64)
} 
```

然后为每种存储类型实现该类。

```
class S3Storage implements Storage {
}

class BlobStorage implements Storage {
}

class DiskStorage implements Storage {
} 
```

最后，您可以随时使用`polymorphism`来更改存储类型:

```
// Save the file in Amazon S3
Storage storage = new S3Storage()

// Save the file in Azure Blob
Storage storage = new BlobStorage()

// Save the file in disk
Storage storage = new DiskStorage() 
```

这就是我们创建接口的原因，因为业务逻辑可以根据客户的需要随时改变。

因此，让我们创建我们的 2 个接口及其实现:

*trainerservice . groovy*

```
package com.pokemon.service

import com.pokemon.entity.Trainer

interface TrainerService {

  List<Trainer> findAll()

  Trainer findById(int id)
} 
```

*trainerserviceimpl . groovy*T2】

```
package com.pokemon.service.impl

import com.pokemon.entity.Trainer
import com.pokemon.service.TrainerService
import com.pokemon.repository.TrainerRepository
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.stereotype.Service

@Service
class TrainerServiceImpl implements TrainerService {

  @Autowired
  private final TrainerRepository trainerRepository

  @Override
  List<Trainer> findAll() {
    trainerRepository.findAll()
  }

  @Override
  Trainer findById(int id) {
    trainerRepository.findById(id)
  }
} 
```

`WildPokemonService`实现非常相似。

我们的实现类有`@Service`注释，因为当另一个类使用`@Autowired`注释时， **Spring** 会为我们创建一个实例。

在`TrainerServiceImpl`中，我们使用`@Autowired`注释来创建一个`TrainerRepository`类的实例。

注释非常重要，因为它们告诉 **Spring** 它们是什么以及如何处理它们。

## 7。控制器类别

最后，是时候创建具有 3 个端点的控制器了:

*   `/trainers` -获得所有训练师
*   `/trainers/{ id }` -找一个特定的教练
*   获得特定训练者的所有口袋妖怪

在`src/main/groovy/com/pokemon`目录下新建一个名为`controller`的文件，然后新建一个名为`TrainerController.groovy` :
的文件

```
package com.pokemon.controller

import com.pokemon.entity.Trainer
import com.pokemon.entity.WildPokemon
import com.pokemon.service.TrainerService
import com.pokemon.service.WildPokemonService
import org.springframework.web.bind.annotation.RestController
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.web.bind.annotation.RequestMethod
import org.springframework.web.bind.annotation.PathVariable

@RestController
@RequestMapping('/trainers')
class TrainerController {

  @Autowired
  private final TrainerService trainerService

  @Autowired
  private final WildPokemonService wildPokemonService

  @RequestMapping(method = RequestMethod.GET)
  List<Trainer> findAll() {
    trainerService.findAll()
  }

  @RequestMapping(value = '/{id}/pokemon', method = RequestMethod.GET)
  List<Trainer> findCaughtPokemon(@PathVariable('id') int id) {
    wildPokemonService.findByTrainer id
  }

  @RequestMapping(value = '/{id}', method = RequestMethod.GET)
  Trainer findById(@PathVariable('id') int id) {
    trainerService.findById id
  }
} 
```

`@RequestMapping`注释配置该控制器中定义的所有端点的前缀。还配置方法类型(GET、POST、PUT、DELETE 等)。

注释为我们创建了服务类的实例。

`@PathVariable` annotation 将指定的 URL 参数传递给方法调用。

最后，让我们使用 **Flyway DB** 将虚拟数据插入 MySQL。创建一个名为`V2__inserting_example_data.sql` :
的新迁移(在`src/main/resources/db/migration`中)

```
INSERT INTO trainers VALUES (1, 'Red', 40), (2, 'Ash Ketchum', 10);

INSERT INTO pokemon VALUES
  (1, 'Bulbasaur', 1), (2, 'Ivysaur', 2), (3, 'Venosaur', 3), (4, 'Charmander', 4),
  (5, 'Charmeleon', 5), (6, 'Charizard', 6), (7, 'Squirtle', 7), (8, 'Wartortle', 8),
  (9, 'Blastoise', 9);

INSERT INTO wild_pokemon VALUES
  (1, 2000, 1, 3), (2, 2100, 4, 6), (7, 2000, 7, 9), (8, 600, 1, 2); 
```

用`gradle bootRun`命令运行项目，进入你最喜欢的浏览器，导航到[http://localhost:8080/trainers/1/pokemon](http://localhost:8080/trainers/1/pokemon)获取:

```
[  {  "id":1,  "combatPower":2000,  "pokemon":{  "id":3,  "name":"Venosaur",  "number":3  },  "trainer":{  "id":1,  "name":"Red",  "level":40  }  },  {  "id":2,  "combatPower":2100,  "pokemon":{  "id":6,  "name":"Charizard",  "number":6  },  "trainer":{  "id":1,  "name":"Red",  "level":40  }  },  {  "id":7,  "combatPower":2000,  "pokemon":{  "id":9,  "name":"Blastoise",  "number":9  },  "trainer":{  "id":1,  "name":"Red",  "level":40  }  }  ] 
```

这是我们最终的项目目录结构:

[![](img/2bf0f0123c73fb76422f5f8985f144dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bU5OJwPC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pwc9ipwwndkgl3baohna.png)

就这些了，伙计们！

非常感谢你阅读这篇长文。

在下一篇文章中，我们将改进我们的口袋妖怪 API，增加更多的功能和东西，如数据传输对象，异常处理程序，招摇，测试等等！

敬请关注。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ jorgeramon ](https://github.com/jorgeramon) / [春天-groovy-口袋妖怪](https://github.com/jorgeramon/spring-groovy-pokemon)

### 使用 Spring Boot + Groovy 的口袋妖怪 API 示例。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 口袋妖怪 API v1.0 版

面向开发者的 Spring Boot + Groovy 示例

## 要求

*   Java 8 或更高版本
*   关系型数据库
*   Gradle

## 配置

编辑文件`application.properties`并更改**您的用户名**、**您的密码**和**您的数据库**的值。

运行该项目将自动创建数据库、表和插入示例数据。

## 运行项目

在终端中键入命令`gradle bootRun`。

</article>

[View on GitHub](https://github.com/jorgeramon/spring-groovy-pokemon)