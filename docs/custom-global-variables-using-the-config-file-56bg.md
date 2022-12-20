# 使用配置文件自定义全局变量

> 原文：<https://dev.to/eecms/custom-global-variables-using-the-config-file-56bg>

我喜欢做的一件事是利用模板标签中的`global_vars`。我非常喜欢在站点的`config.php`文件中保存配置。这样，可以根据环境改变值，或者使用 Ansible 之类的东西来提供和配置您的站点，或者使用 PHP 的`getenv()`函数。有关基于环境的配置的更多阅读，请查看[十二因素应用](https://12factor.net/config)。

下面是我的 ExpressionEngine 网站的几乎所有`config.php`文件中包含的内容的一个例子。每个变量都以`global:`为前缀，类似于我在上一篇文章中给`pre:`变量[加前缀的方式。](https://u.expressionengine.com/article/preload-replace-variables) 

```
$default_global_vars = [
    'global:disabled_params' => 'disable="trackbacks|pagination|member_data"',
    'global:disabled_params_all' => 'disable="trackbacks|pagination|member_data|category_fields|categories|custom_fields"',
    'global:disabled_params_strict' => 'disable="trackbacks|pagination|member_data|category_fields|categories"',
    'global:blog_category_groups' => '2|3|5|6',
    'global:some_api_key' => '98ufahaskdfnasd312',
];

if (!isset($assign_to_config['global_vars'])) {
    $assign_to_config['global_vars'] = [];
}

$assign_to_config['global_vars'] = array_merge(
    $default_global_vars,
    $assign_to_config['global_vars']
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我的模板中，我可以缩短我的标签，并通过对`config.php`文件的一次更改来全局更新网站上的每个条目标签。

```
{exp:channel:entries {global:disabled_params}}
    ...
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

有些人喜欢给所有东西都加上前缀。我见过以`cf_`为前缀的自定义字段，因此它们的模板被填充了`{cf_body}`、`{cf_summary}`或`{cf_related_entries}`。我个人从来不喜欢给字段变量加前缀，除非它是一个前缀，有助于表明它属于哪个通道，比如`{person_first_name}`。`{cf_person_first_name}`对我来说感觉多余，但还是那句话，那是个人喜好。

不管有没有前缀，找到适合你的，选择一个模式并保持一致😊

-
最初由 [Brian Litzinger](https://u.expressionengine.com/author/brian-litzinger) 在[u.expressionengine.com](https://u.expressionengine.com//article/custom-global-variables-using-the-config-file)发表