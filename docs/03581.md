# 拉勒维尔制造视图

> 原文：<https://dev.to/alicannklc/laravel-make-view-51e2>

`php artisan make:command MakeViewCommand`

App
控制台
命令

替换
[MakeViewCommand.php](https://github.com/Alicannklc/Laravel-make-view/blob/master/app/Console/Commands/MakeViewCommand.php)

[Gist Url](https://gist.github.com/umefarooq/ebc617dbf88260db1448)

### 创建视图

```
# Create a view 'home.blade.php' in the default directory
php artisan make:view home

# Create a view 'home.blade.php' in a subdirectory ('pages')
php artisan make:view pages.home 
```

[演示](https://github.com/Alicannklc/Laravel-make-view)