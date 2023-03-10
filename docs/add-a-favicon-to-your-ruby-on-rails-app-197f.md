# 在你的 Ruby on Rails 应用中添加一个 favicon！

> 原文：<https://dev.to/katmuedas/add-a-favicon-to-your-ruby-on-rails-app-197f>

我很乐意分享我对 favicons 的了解以及我是如何在我的项目中使用它们的。

什么是 favicon？

Favicon 是一个与特定网站相关联的小图标，代表浏览器标签、书签列表、搜索历史、搜索广告甚至搜索结果上的网站或品牌。它通常包含标志、品牌的第一个字母或代表内容或业务类型的通用图像。

[![This is a favicon](img/c95f3c263c931397493f747b384a5bc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--67zbegNk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dgmib3f7e1ibzbww231t.png)

为什么网站图标很重要？

*   他们支持品牌意识和认知度。

*   他们节省人们浏览互联网的时间。当用户打开几个选项卡时；网站图标使得区分不同的网站变得容易。

    > 当一个网站不使用收藏夹图标时，你的网络浏览器会在网站名称旁边使用一个小小的空白页面图标。如果在一个专栏中有足够多的空白页，那么选择想要的站点就变得更加困难，因为所有的空白页看起来都是一样的。

*   给网站一个专业的外观。

如何将图标添加到我的项目中？

我正在为 Ruby on Rails 和 [rails_real_favicon](https://github.com/RealFaviconGenerator/rails_real_favicon) gem 使用 [RealFaviconGenerator](https://realfavicongenerator.net/favicon/ruby_on_rails#.XKuuz-tKi_U) 。

[RealFaviconGenerator](https://realfavicongenerator.net/favicon/ruby_on_rails#.XKuuz-tKi_U) 为你生成所有你需要支持大多数浏览器和平台(不仅仅是桌面浏览器)的 favicons 版本。

你需要做的就是:

*   进入 [RealFaviconGenerator](https://realfavicongenerator.net/favicon/ruby_on_rails#.XKuuz-tKi_U) ，提交你的 favicon 图片并制作你的图标。为了获得最佳效果，您的图片应为 260x260 或更高。

*   将 [rails_real_favicon](https://github.com/RealFaviconGenerator/rails_real_favicon) 添加到你的 Gemfile:

```
 group :development do
         gem 'rails_real_favicon'
     end 
```

*   运行`bundle install`。

*   创建一个名为`favicon.json`的新文件，并用:
    填充它(这段代码将在您将 favicon 图片添加到 RealFaviconGenerator 后为您生成。)

```
 {
        "master_picture": "TODO: Path to your master picture",
        "favicon_design": {
            "ios": {
                "picture_aspect": "no_change",
                "assets": {
                    "ios6_and_prior_icons": false,
                    "ios7_and_later_icons": false,
                    "precomposed_icons": false,
                    "declare_only_default_icon": true
                }
            },
            "desktop_browser": [

            ],
            "windows": {
                "picture_aspect": "no_change",
                "background_color": "#da532c",
                "on_conflict": "override",
                "assets": {
                    "windows_80_ie_10_tile": false,
                    "windows_10_ie_11_edge_tiles": {
                        "small": false,
                        "medium": true,
                        "big": false,
                        "rectangle": false
                    }
                }
            },
            "android_chrome": {
                "picture_aspect": "no_change",
                "theme_color": "#ffffff",
                "manifest": {
                    "display": "standalone",
                    "orientation": "not_set",
                    "on_conflict": "override",
                    "declared": true
                },
                "assets": {
                    "legacy_icon": false,
                    "low_resolution_icons": false
                }
            },
            "safari_pinned_tab": {
                "picture_aspect": "black_and_white",
                "threshold": 66.40625,
                "theme_color": "#5bbad5"
            }
        },
        "settings": {
            "scaling_algorithm": "Mitchell",
            "error_on_image_too_small": false,
            "readme_file": false,
            "html_code_file": false,
            "use_path_as_is": false
        }
    } 
```

*   运行`rails generate favicon`来实际创建图标和 HTML 代码。

*   在布局中添加以下代码，以便在页面中插入 HTML 代码。

```
 = render 'application/favicon' 
```

*   不要忘记在`favicon.json`文件中用你的主图片的路径替换`TODO: Path to your master picture`
    。

    我换成了:

    ```
     {
         "master_picture": "app/asseimg/favicon/kat_favicon.png", 
         //rest of the code
     } 
    ```

*   更改`app/asseimg/favicon/site.webmanifest.erb`上的`name`和`short_name`

*   仅此而已！！！🎉

有许多工具和方法可以帮助你在你的项目中添加一个图标。这是我做这件事的方法。希望这是有帮助的，并节省您寻找解决方案的时间。

编码快乐！
***吉*** 🌸