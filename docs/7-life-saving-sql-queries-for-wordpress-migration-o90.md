# Wordpress 迁移的 7 个救命的 SQL 查询

> 原文：<https://dev.to/lucagrandicelli/7-life-saving-sql-queries-for-wordpress-migration-o90>

当你需要手动将一个 Wordpress 安装从一个域迁移到另一个域时，你必须执行一些数据库查询以使新的安装正常工作。

一个常见的误解是，只要在 WP 管理面板中改变`site_url`和`home`值就足够了，但事实并非如此。

这是 Wordpress 用来建立自己的 base_url 的两个核心价值，但是当一个站点已经充满了内容时，这个过程中会涉及到更多的表( *wp_posts、wp_comments 和 wp_postmeta* )，Wordpress 在这些表中存储数据。如果你不更新这些，你将会得到一个所有媒体都指向旧域名以及链接和其他数据的网站。

因此，这里有一些**救命的 SQL 查询**，您需要在手动传输时执行。您只需更改以下占位符，从:

`original.domain.com` **到** `new.domain.com`

> **注意**:如果您正在从本地环境(localhost)迁移站点，请跳过最后一个查询，并执行标记为“从 localhost 环境迁移的备选查询 7”的备选查询。

```
-- Query 1: update the wp_options table
UPDATE wp_options SET option_value = REPLACE(option_value, 'http://original.domain.com', 'https://new.domain.com') WHERE option_name = 'home' OR option_name = 'siteurl';

-- Query 2: update the wp_posts table
UPDATE wp_posts SET post_content = REPLACE (post_content, 'http://old.domain.com', 'https://new.domain.com');

-- Query 3: update the wp_posts table
UPDATE wp_posts SET post_excerpt = REPLACE (post_excerpt, 'http://old.domain.com', 'https://new.domain.com');

-- Query 4: update the wp_postmeta table
UPDATE wp_postmeta SET meta_value = REPLACE (meta_value, 'http://old.domain.com','https://new.domain.com');

-- Query 5: update the wp_comments table
UPDATE wp_comments SET comment_content = REPLACE (comment_content, 'http://old.domain.com', 'https://new.domain.com');

-- Query 6: update the wp_comments table
UPDATE wp_comments SET comment_author_url = REPLACE (comment_author_url, 'http://old.domain.com','https://new.domain.com');

-- Query 7: update the wp_comments table
UPDATE wp_posts SET guid = REPLACE (guid, 'http://old.domain.com', 'https://new.domain.com') WHERE post_type = 'attachment';

-- Alternative Query 7 for migrations from localhost enviroments
-- UPDATE wp_posts SET guid = REPLACE (guid, 'http://old.domain.com', 'https://new.domain.com'); 
```

Enter fullscreen mode Exit fullscreen mode

**重要通知**
将 Wordpress 从一个领域转移到另一个领域时最重要的参考，就是这个抄本页面[这里](https://wordpress.org/support/article/moving-wordpress/)。

还要考虑到，在某些情况下，如前所述，插件可能会在 *wp_postmeta* 表中存储序列化数据。为了防止数据中的潜在中断，请遵循以下法典建议:

如果您在整个数据库中进行搜索和替换来更改 URL，您可能会导致数据序列化问题，因为一些主题和小部件存储的值带有标记的 URL 长度。当这种情况发生变化时，事情就会发生变化。为了避免序列化问题，您有三种选择:

*   使用[天鹅绒布鲁斯更新网址](https://wordpress.org/plugins/velvet-blues-update-urls/)或[更好的搜索替换](https://wordpress.org/plugins/better-search-replace/)插件，如果你可以访问你的仪表板。
*   如果你的主机提供商(或你)已经安装了 WP-CLI，使用 WP-CLI 的搜索替换。
*   使用[搜索并替换 WordPress 数据库脚本](https://interconnectit.com/products/search-and-replace-for-wordpress-databases/)安全地将旧域或路径上的所有实例更改为新的。(*仅当您熟悉数据库管理时才使用此选项**)

**注意**:只在 wp_posts 表上执行搜索和替换。
**注意**:从互连中搜索并替换它是一个第三方脚本

* * *

发现错别字？[制作 PR](https://github.com/lucagrandicelli/blog/tree/master/wordpress/03-%207-life-saving-sql-queries-for-wordpress-migration)