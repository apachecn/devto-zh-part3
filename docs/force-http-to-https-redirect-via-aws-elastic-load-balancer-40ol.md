# 通过 AWS 弹性负载平衡器强制 HTTP 到 HTTPS 重定向

> 原文：<https://dev.to/solrevdev/force-http-to-https-redirect-via-aws-elastic-load-balancer-40ol>

## 通过 AWS 弹性负载平衡器在 IIS 上强制 HTTP 到 HTTPS 重定向

今天，我用一个新的静态站点替换了一个老化的 ASP.NET web forms web 应用程序，这个静态站点现在只是一个带有联系表单的登录页面，这样做需要强制任何不安全的 HTTP 请求到 HTTPS。

有一点要注意的是重定向中的一个错误，问题是`HTTP_X_FORWARDED_PROTO`头也需要和重定向一起转发。

例如:

```
<conditions logicalGrouping="MatchAny">
   <add input="{HTTP_X_FORWARDED_PROTO}" pattern="^http$" />
   <add input="{HTTPS}" pattern="on" />
</conditions> 
```

接下来，我需要将任何将要访问不再存在的`.aspx`页面的用户重定向到一个自定义的 404 HTML 页面未找到页面。

这只是需要添加到`web.config`像这样:

```
 <system.web>
      <compilation targetFramework="4.0" />
      <customErrors mode="On" redirectMode="ResponseRewrite">
         <error statusCode="404" redirect="404.html" />
      </customErrors>
   </system.web>

   <system.webServer>
      <httpErrors errorMode="Custom">
         <remove statusCode="404"/>
         <error statusCode="404" path="/404.html" responseMode="ExecuteURL"/>
      </httpErrors>
   </system.webServer> 
```

所以这是为下一次我必须做类似的事情，这是完整的`web.config`需要在该网站的根文件夹。

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.web>
        <compilation targetFramework="4.0" />
        <customErrors mode="On" redirectMode="ResponseRewrite">
            <error statusCode="404" redirect="404.html" />
        </customErrors>
    </system.web>
    <system.webServer>
        <httpErrors errorMode="Custom">
            <remove statusCode="404"/>
            <error statusCode="404" path="/404.html" responseMode="ExecuteURL"/>
        </httpErrors>
        <rewrite>
            <rules>
                <rule name="HTTPS Rule behind AWS Elastic Load Balancer" stopProcessing="true">
                    <match url="(.*)" ignoreCase="false" />
                    <conditions logicalGrouping="MatchAny">
                        <add input="{HTTP_X_FORWARDED_PROTO}" pattern="^http$" />
                        <add input="{HTTPS}" pattern="on" />
                    </conditions>
                    <action type="Redirect" url="https://{HTTP_HOST}{REQUEST_URI}" redirectType="Found" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration> 
```

为了测试网站的工作情况，我把它输入到 https://www.whynopadlock.com/，这给了我一切都很好的信心。

成功🎉