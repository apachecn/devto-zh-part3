# 使用 webmock 来存根化多个同名的主体参数

> 原文：<https://dev.to/jastkand/use-webmock-for-stubbing-multiple-body-parameters-with-the-same-name-45c3>

如果你使用法拉第来执行请求，很可能你必须使用`Faraday::FlatParamsEncoder`。

```
Faraday.new(url: base_url) do |faraday|
  faraday.request :url_encoded # form-encode POST params
  faraday.options.params_encoder = Faraday::FlatParamsEncoder
  ...
end 
```

它将 JSON 参数转换成去掉方括号的查询字符串，所以它不是将`{ key: ['value1', 'value2'] }`转换成`key[]=value1&key[]=value2`，而是将它转换成`key=value1&key=value2`。这种方法在许多服务中使用，例如在 Twilio API 中。

当您试图使用`webmock`来存根化那个 API 时，问题就发生了。默认情况下,`webmock`不支持 url 编码体中多个同名的参数。于是`key=value1&key=value2`就变成了`key=value1`。以下是相关问题:

*   [https://github.com/bblimke/webmock/issues/227](https://github.com/bblimke/webmock/issues/227)
*   [https://github.com/bblimke/webmock/issues/490](https://github.com/bblimke/webmock/issues/490)
*   [https://github.com/bblimke/webmock/issues/584](https://github.com/bblimke/webmock/issues/584)

webmock repo 中的基本建议是使用:`WebMock::Config.instance.query_values_notation = :flat_array`选项。不过，我更喜欢不同的方法。

当 Faraday 用`Faraday::FlatParamsEncoder`编码主体时，我们可以手动编码主体并将结果传递给 webmock。

```
stub_request(:post, twilio_api_url("Accounts/#{subaccount_sid}/Calls.json"))
  .with(body: Faraday::FlatParamsEncoder.encode(request_params))
  .to_return(body: response_body) 
```

下面的代码块也可以工作，但是为了与法拉第行为保持一致，我使用了上面的代码块。

```
stub_request(:post, twilio_api_url("Accounts/#{subaccount_sid}/Calls.json"))
  .with(body: URI.encode_www_form(request_params))
  .to_return(body: response_body) 
```

我希望这能帮助一些人或者至少节省一些时间。