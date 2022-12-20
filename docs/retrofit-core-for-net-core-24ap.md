# 改装旧件。网络核心

> 原文：<https://dev.to/akashkava/retrofit-core-for-net-core-24ap>

# 改造核心

1.  没有构建步骤
2.  动态服务代理生成器
3.  支持将标题作为属性

# 举例

```
 public interface IBackendService {

    // when set, it will always be sent with
    // every request
    [Header("access-key")]
    AccessKey { get; set; }

    [Get("/location/{ip}")]
    Task<IPInfo> GetLocationInfoAsync([Path("ip")] string ip);

    [Post("/location/{ip}")]
    Task<IPInfo> SaveLocationInfoAsync([Body] IPInfo info);

    // retrive http response for detailed response
    [Get("/video/{id}.mp4")]
    Task<HttpResponseMessage> GetRawResponse([Query("id")] string id);

    [Get("/voice/{id}.mp3")]
    Task<byte[]> GetByteArray([Query("id")] string id);
} 
```

# 用法

```
 var client = RetroClient
       .Create<IBackendService, BaseService>(
           new Uri("base url...") , httpClient); 
```

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[neurospeech](https://github.com/neurospeech)/[retro-core-fit](https://github.com/neurospeech/retro-core-fit)

### REST 库类似于。网络核心

<article class="markdown-body entry-content" itemprop="text">

# 翻新旧件

1.  没有构建步骤
2.  动态服务代理生成器
3.  支持将标题作为属性

# 例子

```
public interface IBackendService {
    // when set, it will always be sent with
    // every request
    [Header("access-key")]
    AccessKey { get; set; }
    [Get("/location/{ip}")]
    Task<IPInfo> GetLocationInfoAsync([Path("ip")] string ip)
    [Post("/location/{ip}")]
    Task<IPInfo> SaveLocationInfoAsync([Body] IPInfo info);

    // retrive http response for detailed response
    [Get("/video/{id}.mp4")]
    Task<HttpResponseMessage> GetRawResponse([Query("id")] string id);

    [Get("/voice/{id}.mp3")]
    Task<byte[]> GetByteArray([Query("id")] string id);
}

```

# 使用

```
    var client = RetroClient.Create<IBackendService, BaseService>( new Uri(
```

…</article>

[View on GitHub](https://github.com/neurospeech/retro-core-fit)