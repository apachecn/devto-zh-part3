# 使用 Dropwizard、MongoDB 和 Docker 构建微服务

> 原文：<https://dev.to/ricdev2/building-microservices-with-dropwizard-mongodb--docker--o30>

### Dropwizard 制造魔法。😎

在本教程中，我将使用 Dropwizard 创建一个微服务，这个框架提供了所有类型的库来构建 web 应用程序。包括的一些库有:

Jetty Server :这是一个开源的 web 服务器，轻量级，易于嵌入到任何应用程序中。

**Jersey** :创建 RESTful web 服务的实现。

Jackson :允许你从 JSON 对象到 POJOs 进行操作，反之亦然。

**度量**:提供关于 HTTP 请求、数据库连接、队列等状态的信息。

Guava :支持许多类来处理集合、验证、字符串等。

Hibernate 验证器:作为 java 对象约束的验证。

JDBI :包装了 JDBC，并提供了一种灵活的方式来操作关系数据库。

**Liquibase** :非常适合数据库中的迁移和 DDL 更改。

### MongoDB NoSQL 数据库等等。🌳

这个面向文档的 NoSQL 数据库(通过像 JSON 这样的文档)结合了关系数据库的一些特性，易于使用，多平台是纵向扩展的最佳选择，具有容错、负载平衡、map reduce 等功能。

#### 本微服务中使用的技术📝

*   OpenJDK 8
*   dock 2 . 0 . 0-MAC 81(MAC 版本)
*   MongoDB 4.0
*   Maven 3.5
*   Mac OS mojave(或任何其他像 windows 或 linux)
*   Nginx 1.15
*   IntelliJ IDEA 2018

好了，让我们开始创建项目，Dropwizard 为我们提供了一个 maven 原型，我们可以从它的网站上使用它，下面是这个命令的一个例子:

```
mvn archetype:generate -DarchetypeGroupId=io.dropwizard.archetypes -DarchetypeArtifactId=java-simple -DarchetypeVersion=1.3.5 -DgroupId=com.demo -DartifactId=dropwizard-mongodb-ms -Dversion=1.0.0-SNAPSHOT -Dname=DropwizardMongoDBMicroservice 
```

Enter fullscreen mode Exit fullscreen mode

打开一个终端，在命令前粘贴，确保已经安装并配置了 maven 和 java。

最终的结构看起来是这样的:

```
dropwizard-mongodb-ms/
├── README.md
├── config.yml
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── demo
    │   │           ├── DropwizardMongoDBMicroserviceApplication.java
    │   │           ├── DropwizardMongoDBMicroserviceConfiguration.java
    │   │           ├── api
    │   │           ├── cli
    │   │           ├── client
    │   │           ├── core
    │   │           ├── db
    │   │           ├── health
    │   │           └── resources
    │   └── resources
    │       ├── assets
    │       └── banner.txt
    └── test
        ├── java
        │   └── com
        │       └── demo
        │           ├── api
        │           ├── client
        │           ├── core
        │           ├── db
        │           └── resources
        └── resources
            └── fixtures 
```

Enter fullscreen mode Exit fullscreen mode

一旦项目被创建，在`pom.xml`文件中添加依赖项:

```
. . .
<properties>
        <dropwizard.version>1.3.5</dropwizard.version>
        <mainClass>com.demo.DropwizardMongoDBMicroserviceApplication</mainClass>
        <mongodb.version>3.8.2</mongodb.version>
        <jdk.version>1.8</jdk.version>
        <dropwizard.swagger.version>1.0.6-1</dropwizard.swagger.version>
        <mockito.core.version>2.23.0</mockito.core.version>
</properties>
. . .
<dependency>
    <groupId>io.dropwizard</groupId>
    <artifactId>dropwizard-core</artifactId>
</dependency>
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongodb-driver-sync</artifactId>
    <version>${mongodb.version}</version>
</dependency>

<dependency>
    <groupId>com.smoketurner</groupId>
    <artifactId>dropwizard-swagger</artifactId>
    <version>${dropwizard.swagger.version}</version>
</dependency>

<!-- Testing -->
<dependency>
    <groupId>io.dropwizard</groupId>
    <artifactId>dropwizard-testing</artifactId>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>${mockito.core.version}</version>
    <scope>test</scope>
</dependency>
<!-- Testing -->
. . . 
```

Enter fullscreen mode Exit fullscreen mode

我在文件`configuration.yaml`中添加了一些配置，看起来像这样:

```
server:
  maxThreads: 512
  applicationContextPath: /dropwizard-mongodb-ms
  applicationConnectors:
    - type: http
      port: 8080
  adminConnectors:
    - type: http
      port: 8081

logging:
  level: INFO
  loggers:
    com.demo: INFO

#You can choose the user and password what you want.
mongoDBConnection:
  credentials:
    username: "user_donuts" 
    password: "pAsw0Rd"
  seeds:
    - host: "mongodb"
      port: 27017
  database: "donuts"

swagger:
  basePath: /dropwizard-mongodb-ms
  resourcePackage: com.demo.resources
  scan: true
  info:
    version: "1.0.0"
    title: "Donuts  API  CRUD"
    description: "A  simple  API  used  for  expose  CRUD  operation  on  MongoDB  collection"
    termsOfService: "http://swagger.io/terms/"
    contact:
      name: "Donuts  API  "
    license:
      name: "Rich  Lopez" 
```

Enter fullscreen mode Exit fullscreen mode

configuration.yml 映射到一个扩展了`io.dropwizard.Configuration`的类，如下所示:

```
package com.demo;

import com.demo.db.configuration.MongoDBConnection;
import com.fasterxml.jackson.annotation.JsonProperty;

import io.dropwizard.Configuration;
import io.federecio.dropwizard.swagger.SwaggerBundleConfiguration;

public class DropwizardMongoDBMicroserviceConfiguration extends Configuration {

    /**
     * The data configuration for MongoDB.
     */

    private MongoDBConnection mongoDBConnection;

    @JsonProperty("swagger")
    private SwaggerBundleConfiguration swaggerBundleConfiguration;

    //Getters and setters
} 
```

Enter fullscreen mode Exit fullscreen mode

这个微服务将公开一个基于 CRUD(创建、读取、更新、删除)API REST。我已经选择了一个产品，如甜甜圈(🍩 🤤)，你可以在我的 Github 资源库中找到本教程末尾的部分代码。

我使用`com.mongodb.client.MongoCollection`接口来访问名为`donuts`的集合，以便在操作中有更多的粒度，所以我创建了一个 DAO(数据访问对象)来操纵 CRUD 操作。

```
package com.demo.db.daos;
//imports ... 
public class DonutDAO {

    /** The collection of Donuts */
    final MongoCollection<Document> donutCollection;

    /**
     * Constructor.
     *
     * @param donutCollection the collection of donuts.
     */
    public DonutDAO(final MongoCollection<Document> donutCollection) {
        this.donutCollection = donutCollection;
    }

    /**
     * Find all donuts.
     *
     * @return the donuts.
     */
    public List<Donut> getAll() {
        final MongoCursor<Document> donuts = donutCollection.find().iterator();
        final List<Donut> donutsFind = new ArrayList<>();
        try {
            while (donuts.hasNext()) {
                final Document donut = donuts.next();
                donutsFind.add(DonutMapper.map(donut));
            }
        } finally {
            donuts.close();
        }
        return donutsFind;
    }

    /**
     * Get one document find in other case return null.
     *
     * @param id the identifier for find.
     * @return the Donut find.
     */
    public Donut getOne(final ObjectId id) {
        final Optional<Document> donutFind = Optional.ofNullable(donutCollection.find(new Document("_id", id)).first());
        return donutFind.isPresent() ? DonutMapper.map(donutFind.get()) : null;
    }

    public void save(final Donut donut){
        final Document saveDonut = new Document("price", donut.getPrice())
                                      .append("flavor", donut.getFlavor());
        donutCollection.insertOne(saveDonut);
    }

    /**
     * Update a register.
     *
     * @param id the identifier.
     * @param donut the object to update.
     */
    public void update(final ObjectId id, final Donut donut) {
        donutCollection.updateOne(new Document("_id", id),
                new Document("$set", new Document("price", donut.getPrice())
                        .append("flavor", donut.getFlavor()))
        );
    }

    /**
     * Delete a register.
     * @param id    the identifier.
     */
    public void delete(final ObjectId id){
        donutCollection.deleteOne(new Document("_id", id));
    } 
```

Enter fullscreen mode Exit fullscreen mode

为了映射来自数据库的信息，我使用了一个实用程序类来过滤 MongoDB 到 POJO 的字段。

```
package com.demo.util;

import com.demo.api.Donut;
import org.bson.Document;

public class DonutMapper {

    /**
     * Map objects {@link Document} to {@link Donut}.
     *
     * @param donutDocument the information document.
     * @return A object {@link Donut}.
     */
    public static Donut map(final Document donutDocument) {
        final Donut donut = new Donut();
        donut.setId(donutDocument.getObjectId("_id"));
        donut.setFlavor(donutDocument.getString("flavor"));
        donut.setPrice(donutDocument.getDouble("price"));
        return donut;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后创建一个 POJO 来操作 MongoDB 上的信息。

```
package com.demo.api;
//imports ...
public class Donut implements Serializable {

    /** The id.*/
    @JsonSerialize(using = ObjectIdSerializer.class)
    private ObjectId id;

    /** The price. */
    @NotNull
    private double price;

    /** The principal flavor.*/
    @NotNull
    private String flavor;

    /**Constructor.*/
    public Donut() {
    }

//getters & setters
//hashcode, equals and toString methods 
```

Enter fullscreen mode Exit fullscreen mode

此类仅用于响应端点。

```
package com.demo.api;
//imports ...
public class Response {
    /** The message.*/
    private String message;

    /** Constructor.*/
    public Response() {
    }
//getters & setters
//hashcode, equals and toString methods 
```

Enter fullscreen mode Exit fullscreen mode

这里有更多使用 Jackson 将`org.bson.types.ObjectId`转换为`String`对象的实用程序类。

```
package com.demo.util;

import java.io.IOException;

import org.bson.types.ObjectId;

import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;

public class ObjectIdSerializer extends JsonSerializer<ObjectId> {
    @Override
    public void serialize(final ObjectId objectId, final JsonGenerator jsonGenerator,
                          final SerializerProvider serializerProvider) throws IOException {
        jsonGenerator.writeString(objectId.toString());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

用于将`String`对象转换为`char`的数组。

```
package com.demo.util;

import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;

import java.io.IOException;

public class PasswordSerializer extends JsonSerializer<String> {
    @Override
    public void serialize(final String input, final JsonGenerator jsonGenerator,
                          final SerializerProvider serializerProvider) throws IOException {
        jsonGenerator.writeString(input.toCharArray(), 0, input.toCharArray().length);
    }

    @Override
    public Class<String> handledType() {
        return String.class;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这些 POJOs 将信息与`configuration.yml`进行映射。

```
package com.demo.db.configuration;

import java.util.Arrays;
import java.util.Objects;

import com.demo.util.PasswordSerializer;
import com.fasterxml.jackson.databind.annotation.JsonSerialize;

/**
 * This class is used for credentials.
 * @version 1.0.0
 * @since 1.0.0
 * @author Rich Lopez
 */
public class Credentials {

    /** The user name.*/
    private String username;

    /** The password.*/
    @JsonSerialize(using = PasswordSerializer.class)
    private char[] password;

    //getters, setters, hashcode and equals methods.
} 
```

Enter fullscreen mode Exit fullscreen mode

```
package com.demo.db.configuration;

import java.util.Objects;

public class Seed {

    /** The host.*/
    private String host;

    /** The port.*/
    private int port;
    //getters, setters, hashcode and equals methods.
} 
```

Enter fullscreen mode Exit fullscreen mode

```
package com.demo.db.configuration;

import java.util.List;

public class MongoDBConnection {

    /**
     * The credentials user and password.
     */
    private Credentials credentials;

    /**
     * The lis of seeds.
     */
    private List<Seed> seeds;

    /**
     * The db.
     */
    private String database;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了管理与 MongoDB 的连接，我们需要将来自 yaml 文件配置的信息映射到这个类`com.demo.db.configuration.Credentials`、`com.demo.db.configuration.MongoDBConnection`、`com.demo.db.configuration.Seed`。

类`com.demo.db.MongoDBFactoryConnection`使用 yaml 文件中提供的选项为 MongoDB 创建客户端，创建实例的方法如下:

```
package com.demo.db;
//imports ...
public class MongoDBFactoryConnection {

    /** The configuration for connect to MongoDB Server.*/
    private MongoDBConnection mongoDBConnection;

    /**
     * Constructor.
     *
     * @param mongoDBConnection the mongoDB connection data.
     */
    public MongoDBFactoryConnection(final MongoDBConnection mongoDBConnection) {
        this.mongoDBConnection = mongoDBConnection;
    }

    /**
     * Gets the connection to MongoDB.
     *
     * @return the mongo Client.
     */
    public MongoClient getClient() {
        LOGGER.info("Creating mongoDB client.");
        final Credentials configCredentials = mongoDBConnection.getCredentials();

        final MongoCredential credentials = MongoCredential.createCredential(
                configCredentials.getUsername(),
                mongoDBConnection.getDatabase(),
                configCredentials.getPassword());

        final MongoClient client = MongoClients.create(
                MongoClientSettings.builder()
                        .credential(credentials)
                        .applyToClusterSettings(builder -> builder.hosts(getServers())).build()
        );

        return client;
    }

    /**
     * Map the object {@link Seed} to objects {@link ServerAddress} that contain the information of servers.
     *
     * @return the list of servers.
     */
    private List<ServerAddress> getServers() {
        final List<Seed> seeds = mongoDBConnection.getSeeds();
        return seeds.stream()
                .map(seed -> {
                    final ServerAddress serverAddress = new ServerAddress(seed.getHost(), seed.getPort());
                    return serverAddress;
                })
                .collect(Collectors.toList());
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 Dropwizard 生命周期中，对象可以由接口`io.dropwizard.lifecycle.Managed`和类`io.dropwizard.lifecycle.MongoDBManaged`来管理。

```
package com.demo.db;
//imports ...
public class MongoDBManaged implements Managed {

    /** The mongoDB client.*/
    private MongoClient mongoClient;

    /**
     * Constructor.
     * @param mongoClient   the mongoDB client.
     */
    public MongoDBManaged(final MongoClient mongoClient) {
        this.mongoClient = mongoClient;
    }

    @Override
    public void start() throws Exception {
    }

    @Override
    public void stop() throws Exception {
        mongoClient.close();
    } 
```

Enter fullscreen mode Exit fullscreen mode

另一个重要的类是 Healthcheck，因为创建这个类只需要从`com.codahale.metrics.health.HealthCheck`扩展并实现方法`check`。

```
package com.demo.health;
//imports ...
public class DropwizardMongoDBHealthCheck extends HealthCheck {
     /** A client of MongoDB.*/
    private MongoClient mongoClient;

    /**
     * Constructor.
     *
     * @param mongoClient the mongo client.
     */
    public DropwizardMongoDBHealthCheck(final MongoClient mongoClient) {
        this.mongoClient = mongoClient;
    }
    @Override
    protected Result check() {
        try {
            final Document document = mongoClient.getDatabase("donuts").runCommand(new Document("buildInfo", 1));
            if (document == null) {
                return Result.unhealthy("Can not perform operation buildInfo in Database.");
            }
        } catch (final Exception e) {
            return Result.unhealthy("Can not get the information from database.");
        }
        return Result.healthy();
    } 
```

Enter fullscreen mode Exit fullscreen mode

这个微服务的入口点是类`com.demo.DropwizardMongoDBMicroserviceApplication`加载所有的包配置、初始化类等。该类必须从`io.dropwizard.Application`扩展，并由扩展`io.dropwizard.Configuration`的类来参数化。

```
package com.demo;

import com.demo.db.MongoDBFactoryConnection;
import com.demo.db.daos.DonutDAO;
import com.demo.db.MongoDBManaged;
import com.demo.health.DropwizardMongoDBHealthCheck;
import com.demo.resources.DonutResource;

import io.dropwizard.Application;
import io.dropwizard.setup.Bootstrap;
import io.dropwizard.setup.Environment;
import io.federecio.dropwizard.swagger.SwaggerBundle;
import io.federecio.dropwizard.swagger.SwaggerBundleConfiguration;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class DropwizardMongoDBMicroserviceApplication extends Application<DropwizardMongoDBMicroserviceConfiguration> {

    /**
     * Logger class.
     */
    private static final Logger LOGGER = LoggerFactory.getLogger(DropwizardMongoDBMicroserviceApplication.class);

    /**
     * Entry point for start Application.
     *
     * @param args the args.
     * @throws Exception when the app can not start.
     */
    public static void main(final String[] args) throws Exception {
        LOGGER.info("Start application.");
        new DropwizardMongoDBMicroserviceApplication().run(args);
    }

    @Override
    public String getName() {
        return "DropwizardMongoDBMicroservice";
    }

    @Override
    public void initialize(final Bootstrap<DropwizardMongoDBMicroserviceConfiguration> bootstrap) {
        bootstrap.addBundle(new SwaggerBundle<DropwizardMongoDBMicroserviceConfiguration>() {
            @Override
            protected SwaggerBundleConfiguration getSwaggerBundleConfiguration(
                        final DropwizardMongoDBMicroserviceConfiguration dropwizardMongoDBMicroserviceConfiguration) {
                return dropwizardMongoDBMicroserviceConfiguration.getSwaggerBundleConfiguration();
            }
        });
    }

    @Override
    public void run(final DropwizardMongoDBMicroserviceConfiguration configuration,
                    final Environment environment) {

        final MongoDBFactoryConnection mongoDBManagerConn = new MongoDBFactoryConnection(configuration.getMongoDBConnection());

        final MongoDBManaged mongoDBManaged = new MongoDBManaged(mongoDBManagerConn.getClient());

        final DonutDAO donutDAO = new DonutDAO(mongoDBManagerConn.getClient()
                .getDatabase(configuration.getMongoDBConnection().getDatabase())
                .getCollection("donuts"));

        environment.lifecycle().manage(mongoDBManaged);
        environment.jersey().register(new DonutResource(donutDAO));
        environment.healthChecks().register("DropwizardMongoDBHealthCheck",
                new DropwizardMongoDBHealthCheck(mongoDBManagerConn.getClient()));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用命令构建项目:

`$ mvn clean package`

在启动应用程序之前，需要有数据库，您可以从网站下载 MongoDB 或使用 DockerHub 中的 docker 映像。

一旦下载了 MongoDB 的镜像，启动一个新的容器:

`$ docker run --name mongodb -d -p 27017:27017 mongo`

**注意:**确保创建一个卷来维护信息的持久性。

输入容器并键入以下命令:

`$ docker exec -it [container name or id] /bin/bash`
`$ mongo`
`$ use donuts`

默认情况下，MongoDB 将身份验证机制设置为 SCRAM。

现在，使用以下命令启动应用程序:

`$ java -jar target/dropwizard-mongodb-ms-1.0.0-SNAPSHOT.jar server configuration.yml`

一旦我们创建了用户和数据库，我们就可以在下面的 URL 中输入 swagger 文档:

*[http://localhost:8080/drop wizard-MongoDB-ms/swagger](http://localhost:8080/dropwizard-mongodb-ms/swagger)T3】*

[![Swagger Documentation](img/297e9442bef9f8ff5dd1128edcc02224.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jhquB277--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ricdev2/dropwizard-mongodb-ms/master/imgs/swagger.png)

### 使用 docker 撰写🐳

每天使用 docker 的原因之一是因为它为我们提供了一个独特的环境，并且所有开发人员都保持依赖关系、数据库、web 服务器等同步。构建和部署工件的简单方法减少了交付时间。

我使用 docker 来启动应用程序和 MongoDB 服务器，此外我还添加了 *Nginx 代理*来接收请求，这是 docker 编写文件的一个例子:

```
version: '3'
services:
  mongodb:
    image: mongo
    restart: always
    container_name: mongodb
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: admin
    ports:
      - 27017:27017
    networks:
      - dropw-mongodb-ntw

  nginx:
    image: nginx
    container_name: nginx
    volumes:
    - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
    - "8080:80"
    - "443:443"
    networks:
    - dropw-mongodb-ntw

  dropw-ms:
    image: openjdk:8-jre
    container_name: dropw-ms
    volumes:
    - ./target/dropwizard-mongodb-ms-1.0.0-SNAPSHOT.jar:/microservice/dropwizard-mongodb-ms-1.0.0-SNAPSHOT.jar
    - ./configuration.yml:/microservice/configuration.yml
    working_dir: /microservice
    command: ["java", "-jar", "dropwizard-mongodb-ms-1.0.0-SNAPSHOT.jar", "server", "configuration.yml"]
    ports:
    - "8090:8080"
    - "8091:8081"
    networks:
    - dropw-mongodb-ntw

networks:
  dropw-mongodb-ntw:
    external:
      name: dropw-mongodb-ntw 
```

Enter fullscreen mode Exit fullscreen mode

我们使用以下命令创建 docker 网络:

`docker network create dropw-mongodb-ntw`

使用以下命令构建项目:

`$ mvn clean package`

开始坞站组成:

`$ docker-compose up`

一旦我们创建了为数据库创建用户所需的所有服务，我们将使用以下命令访问容器。

`$ docker exec -it [container name or id] /bin/bash`
`$ mongo`
`> use donuts`

*[http://localhost:8080/drop wizard-MongoDB-ms/swagger](http://localhost:8080/dropwizard-mongodb-ms/swagger)T3】*

## 结论

Dropwizard 为我们提供了几个工具，让我们可以轻松地创建微服务。您可以通过示例与其他技术集成，如 MongoDB、Docker、Nginx。请记住，这一切都取决于需求和要解决的问题，但对我来说，这是一个很好的开始和分享经验。

Github 库:[https://github.com/ricdev2/dropwizard-mongodb-ms](https://github.com/ricdev2/dropwizard-mongodb-ms)