# 如何用 Buffer 和 Cron 自动分享你的 Wordpress 文章

> 原文：<https://dev.to/nicolrx/how-to-automatically-share-your-wordpress-articles-on-social-media-with-buffer-and-cron-3d77>

[![Wordpress Buffer Cron](img/5354b24b72e329622813867c9a8199a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QJtBD3yq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ol73s93ufo8lpdiw531.png)

对于写了很多文章的博主来说，在社交媒体上分享文章是一件令人头疼的事情。我们通常在出版后这样做，然后忘记定期再次分享，因此失去了一大群潜在的读者。

这就是为什么我决定使用 [Cron](https://codex.wordpress.org/Function_Reference/wp_cron) 和[Buffer API](https://buffer.com/developers/api)来自动化我的 Wordpress 博客的社交媒体发布。Wordpress 中的 Cron 允许你安排基于时间的任务。Buffer 是一种在社交媒体上安排帖子的服务。

## 让我们设置新的 cron 作业

首先，我们需要设置新的 Wordpress cron 作业。cron 系统有默认的间隔`hourly`、`twicedaily`和`daily`。但是，您可以定义自己的时间间隔:

```
// We add a new interval running the task every three hour
add_filter( 'cron_schedules', 'example_add_cron_interval' );
function example_add_cron_interval( $schedules ) {
    $schedules['three_hours'] = array(
        'interval' => 10800,
        'display'  => esc_html__( 'Every Three Hours' ),
    );

    return $schedules;
} 
```

参数`interval`是以秒为单位的持续时间的整数。有了这段代码，您现在可以使用新的 cron 间隔“three_hours”。

然后，我们需要添加新的 cron 任务。为此，我们可以在管理菜单中添加一个按钮:

```
function buffer_init_cron() {
    if ( ! wp_next_scheduled( 'buffer_cron_hook' ) ) {
    wp_schedule_event( time(), 'three_hours', 'buffer_cron_hook' );
        echo '<pre>'; print_r( _get_cron_array() ); echo '</pre>'; // to confirm that the new cron job is scheduled
    }
}

function add_buffer_menu_item_cron() {
  // $page_title, $menu_title, $capability, $menu_slug, $callback_function
  add_menu_page(__('Schedule Cron Buffer Posts'), __('Schedule Cron Buffer Posts'), 'manage_options', 'buffer_menu1', 'buffer_init_cron');
}
add_action('admin_menu', 'add_buffer_menu_item_cron'); 
```

按下管理菜单中的“调度 cron 缓冲区发布”按钮将初始化 Cron 作业。

`wp_schedule_event`接受三个参数:您希望事件发生的第一次时间、重复周期(在这种情况下是三个小时)，以及要调用的将 cron 链接到要执行的函数的函数。对于最后一个参数，我们需要添加以下操作:

```
add_action( 'buffer_cron_hook', 'buffer_schedule_posts' ); 
```

如果您希望能够管理您的 cron 间隔和 cron 作业，您可以安装 [WP Crontrol 插件](https://wordpress.org/plugins/wp-crontrol/)。这个插件将允许你创建新的时间间隔，直接在管理面板中运行你的 cron 任务，创建和删除 cron 任务。

## 创建函数，用缓冲 API 调度帖子

在`buffer_schedule_posts`函数中，我们想要得到一个随机的帖子:

```
 $args = array(
                'post_type' => 'post',
                'orderby'   => 'rand',
                'posts_per_page' => 1,
                );

        $the_query = new WP_Query( $args ); 
```

然后，我们希望提取相关信息，以便创建社交媒体出版物的文本。在我的例子中，我希望获得文章的标题、描述社交媒体富人卡的摘录，当然还有 URL。

```
 if ( $the_query->have_posts() ) {
            while ( $the_query->have_posts() ) {
                $the_query->the_post();
                $page_title = get_the_title();
                $page_description = get_the_excerpt();
                $link = get_permalink();
                $text = $page_title . " " . $link;
            }
        } 
```

缓冲 API 为[提供了一个访问令牌](https://buffer.com/developers/api/oauth)。我用上面的代码创建了一个函数，以便返回一个包含 API 运行所需的 POST 参数的`$fields`变量。

```
 // we get a random pages
    function wpb_rand_posts() {

        $args = array(
                'post_type' => 'post',
                'orderby'   => 'rand',
                'posts_per_page' => 1,
                );

        $the_query = new WP_Query( $args );

        if ( $the_query->have_posts() ) {

            while ( $the_query->have_posts() ) {
                $the_query->the_post();
                $page_title = get_the_title();
                $page_description = get_the_excerpt();
                $link = get_permalink();
                $text = $page_title . " " . $link;

                $fields = "access_token=YOUR_TOKEN&text=" . $text . "&profile_ids[]=YOUR_PROFILE_ID&media[link]=".$link."&media[description]=".$page_description."&now=false";

        }
            return $fields;
            /* Restore original Post Data */
            wp_reset_postdata();
        }
    } 
```

太好了，我们得到了一个随机页面，上面有社交媒体出版物所需的所有信息！最后一步是使用 Curl 执行缓冲 API 调用:

```
 $ch = curl_init();

    $page_fields = wpb_rand_posts();

    curl_setopt($ch, CURLOPT_URL,"https://api.bufferapp.com/1/updates/create.json");
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $page_fields);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/x-www-form-urlencoded'));

    // Receive server response ...
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

    $server_output = curl_exec($ch);

    curl_close ($ch); 
```

瞧啊。我们有自己的`buffer_schedule_posts`函数，每三个小时运行一次，通过 Buffer 发布一篇文章。坐下来放松一下😎