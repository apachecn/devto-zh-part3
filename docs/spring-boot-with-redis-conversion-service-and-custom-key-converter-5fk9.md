# 具有 Redis 转换服务和自定义密钥转换器的 Spring Boot

> 原文：<https://dev.to/tech_sam/spring-boot-with-redis-conversion-service-and-custom-key-converter-5fk9>

这篇文章是关于使用 spring-data-redis 的 Spring-boot 和 Redis 集成的。Spring Data Redis 是 Spring 数据平台对流行的内存数据结构存储的抽象。这个项目的完整源代码可以在 [GitHub](https://github.com/Earth-43/Spring-Boot-Redis-ConversionServive) 上获得。让我们开门见山地说吧。

我假设您已经熟悉 spring boot 和 redis，并且已经选择 redis 作为项目的缓存存储。

在一些场景中，我们必须处理多租户应用程序，或者我们希望在 Redis 中缓存多个用户数据，那么为了避免数据冲突和更好的应用程序架构，我们可以利用 spring redis 数据转换服务。

当我们必须保存所有用户共有的数据时，下面的逻辑是正确的。

```
String cacheKey = “user-data”; 
   Cache cache = cacheManager.getCache(cacheKey);
   Cache.ValueWrapper result = cache != null ? cache.get(cacheKey) : null;
   if(result.get()!=null){
        // get the data and proceed 
    }else{
        cache.put(cacheKey, userData);
    } 
```

但是，如果当您想要保存个人用户数据时，上面的逻辑将不起作用。然后，我们可以使用带有自定义密钥转换器的转换服务，它允许我们向用户数据(如用户 id)添加动态前缀。

### #步骤集成 spring 数据 redis

1.  将以下依赖项添加到 build.gradle 文件中，如果是 maven，则添加到 pom.xml 中

`compile group: ‘org.springframework.data’, name: ‘spring-data-redis’, version: ‘2.1.5.RELEASE’
compile group: ‘org.apache.commons’, name: ‘commons-pool2’, version: ‘2.0’
compile group: ‘redis.clients’, name: ‘jedis’, version: ‘2.9.3’`

1.  创建一个自定义键和转换器类，如。

```
 @AllArgsConstructor
 public class UserCacheKey {
 private String key;
} 
```

@Getter 和@AllArgsConstructor 注释是 Lombok 项目的一部分，我们可以在其他文章中介绍。如果您不知道 Lombok，那么添加默认的 setter getter 方法和带有关键参数的构造函数。

```
 public class UserCacheKeyConverter implements Converter<UserCacheKey, String> {
  private UserServive userService;
  public UserCacheKeyConverter(UserServive userService) {
   this.userService = userService;
 }
 @Nullable
 @Override
 public String convert(UserCacheKey userCacheKey) {
 String userId = userService.getUserId(userCacheKey);
 return String.format(“%s_%s”, userId , userCacheKey.getKey());
 }
} 
```

1.  创建一个 Redis 配置文件。

```
@Bean(name = “userCacheManager”)
 public RedisCacheManager userCacheManager(RedisConnectionFactory 
 connectionFactory, UserService userService) {
 RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig();
 DefaultFormattingConversionService conversionService = (DefaultFormattingConversionService) redisCacheConfiguration.getConversionService();
 conversionService.addConverter(UserCacheKey.class, String.class, new UserCacheKeyConverter(userService));
 redisCacheConfiguration
 .entryTtl(Duration.ofSeconds(1800)).withConversionService(conversionService)
 .disableCachingNullValues();
 return RedisCacheManager.builder(connectionFactory)
 .cacheDefaults(redisCacheConfiguration)
 .withInitialCacheConfigurations(Collections.singletonMap(“user-cache”, redisCacheConfiguration))
 .build();
 } 
```

因此，我们在这里创建一个 RedisCacheManager，它将只处理与用户缓存相关的操作。我们可以在任何地方将这个 RedisCacheManager 作为命名 bean userCacheManager(@ Qualifier(userCacheManager))注入，并执行类似
的相关操作

```
Cache cache = userCacheManager.getCache(“user-cache”);
 UserCacheKey cacheKey = new UserCacheKey(“some-user-data”);
 Cache.ValueWrapper result = cache != null ? cache.get(cacheKey) : null;
 if(result.get()!=null){
 // get the data and proceed 
 }else{
 cache.put(cacheKey, userData);
 } 
```

因此，无论何时调用 cache.get 方法，它都会首先调用 cache converter 并转换密钥。它将帮助我们抽象缓存逻辑并降低复杂性。

如果您觉得这很有帮助，或者想聊聊与 web 或移动开发相关的任何事情；在下面留言。我很乐意讨论。敬请关注更多内容。