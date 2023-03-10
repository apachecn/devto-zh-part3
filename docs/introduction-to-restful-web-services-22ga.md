# Restful web 服务简介

> 原文：<https://dev.to/arpitmandliya/introduction-to-restful-web-services-22ga>

在本帖中，我们将看到 RESTful web 服务介绍。

REST 是罗伊·菲尔丁在 2000 年的博士论文中提出的一种建筑风格。在 web 服务术语中，表述性状态转移(REST)是一种无状态的客户端-服务器架构，其中 web 服务被视为资源，可以通过它们的 URIs 来标识。想要使用这些资源的 Web 服务客户端通过一组全局定义的远程方法进行访问，这些方法描述了要在资源上执行的操作。

它由两个组件组成:提供资源访问的 REST 服务器和访问和修改 REST 资源的 REST 客户机。

在 REST 架构风格中，客户机和服务器通过使用标准化的接口和协议来交换资源的表示。REST 不是特定于协议的，但是当人们谈论 REST 时，他们通常指的是 HTTP 上的 REST。来自服务器的响应被认为是资源的表示。这种表示可以从一个资源或多个资源中生成。
[![](img/6c7cfb535325270d464b6945c22ea947.png)](http://www.java2blog.com/wp-content/uploads/2013/04/RestfuArchitecture.jpg) 
REST 允许资源有不同的表示，如 xml、json 等。rest 客户端可以通过 HTTP 协议请求特定的表示。

### HTTP 方法:

RESTful web 服务使用 HTTP 协议方法来执行操作。方法有:

*   **GET** :定义了一个没有副作用的资源读取权限。该操作是幂等的，即它们可以被应用多次而不改变结果
*   **PUT** :一般用于更新资源。它也必须是幂等的。
*   **删除**:删除资源。这些操作是等幂的，即它们可以重复而不会导致不同的结果。
*   **POST** :用于新建一个资源。它不是幂等的。

**幂等**表示多次成功请求的结果在初始应用
后不会改变资源的状态例如:
删除是幂等的方法，因为当你第一次使用删除时，它会删除资源(初始应用)，但在那之后，所有其他请求将没有结果，因为资源已经被删除。

Post 不是等幂方法，因为当您使用 post 创建资源时，它将为每个新请求不断创建资源，因此多个成功请求的结果将不会相同。

## RESTful web 服务的特性:

**通过 URI 的资源识别**:资源通过它们的 URIs(通常是互联网上的链接)来识别。因此，客户端可以使用资源的 URIs 直接访问 RESTful Web 服务(就像您在浏览器的地址栏中放入一个网站地址并获得一些表示作为响应一样)。

**统一接口**:使用一组固定的四个创建、读取、更新、删除操作来操纵资源:上传、获取、发布和删除。

**客户机-服务器**:这种限制背后的原因是明确的分离顾虑。分离客户机和服务器之间的关系有助于提高客户机的可移植性和服务器组件的可伸缩性。

**无状态**:从客户端到服务器的每个请求必须包含理解请求所需的所有信息，并且不能利用服务器上存储的任何上下文。

**缓存**:为了提高网络效率，响应必须能够被标记为可缓存或不可缓存。

**命名资源** -系统由使用 URL 命名的资源组成。

**互连的资源表示** -资源的表示使用 URL 互连，从而使客户端能够从一种状态前进到另一种状态。

**分层组件** -可以在客户端和资源之间插入中介，如代理服务器、缓存服务器、网关等，以支持性能、安全性等。

**自描述消息**:资源从它们的表示中分离出来，因此它们的内容可以以多种格式访问，比如 HTML、XML、纯文本、PDF、JPEG、JSON 等等。

你也可以查看 [Restful 面试问题，包括给初学者和有经验者的答案](http://www.java2blog.com/2016/06/restful-web-services-interview-questions.html)

## Web 服务教程内容:

*   [Java 中的 Web 服务](http://java2blog.com/2013/03/web-service-tutorial.html)
*   [Web 服务面试问题](http://www.java2blog.com/2016/06/web-services-interview-questions.html)
*   [SOAP web 服务介绍](http://java2blog.com/2013/03/soap-web-service-tutorial.html)
*   [RESTful web 服务介绍](http://java2blog.com/2013/04/restful-web-service-tutorial.html)
*   【SOAP 和 REST web 服务的区别
*   [使用 eclipse 的 Java SOAP web 服务示例](http://java2blog.com/2013/03/soap-web-service-example-in-java-using.html)
*   [JAX-WS web 服务 eclipse 教程](http://java2blog.com/2013/03/jaxws-web-service-eclipse-tutorial.html)
*   在 tomcat 上部署 JAX-WS web 服务
*   [使用 jersey 在 java(JAX-RS)中创建 RESTful web 服务](http://java2blog.com/2013/04/create-restful-web-servicesjax-rs-using.html)
*   [RESTful web 服务](http://www.java2blog.com/2015/10/restful-web-services-jaxrs-json-example.html)
*   [jajson 使用运动衫的示例](http://www.java2blog.com/2015/10/restful-web-services-jaxrs-json-example.html)
*   [RESTful web 服务](http://www.java2blog.com/2016/03/restful-web-services-jaxrs-crud-example.html)
*   [使用 jersey 的 JAXRS CRUD 示例](http://www.java2blog.com/2016/03/restful-web-services-jaxrs-crud-example.html)
*   [AngularJS RESTful web 服务](http://www.java2blog.com/2016/03/angularjs-restful-web-service-example.html)
*   [使用$http 的 JAXRS CRUD 示例](http://www.java2blog.com/2016/03/angularjs-restful-web-service-example.html)
*   [RESTful Web 服务(JAX-RS) @QueryParam 示例](http://www.java2blog.com/2015/10/restful-web-services-jax-rs-queryparam.html)
*   [弹簧座简单例子](http://www.java2blog.com/2015/09/spring-restful-web-services-example.html)
*   [弹簧座 json 示例](http://www.java2blog.com/2015/09/spring-restful-web-services-json-example.html)
*   [Spring Rest xml 示例](http://www.java2blog.com/2016/08/spring-restful-web-services-xml-example.html)
*   [弹簧座积垢示例](http://www.java2blog.com/2016/04/spring-restful-web-services-crud-example.html)