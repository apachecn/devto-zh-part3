# 使用 WP REST API 设置您自己的端点。

> 原文：<https://dev.to/rinstarskykujat/set-up-your-own-endpoint-using-the-wp-rest-api-3oob>

# 使用 WP REST API 设置自己的端点。

### 参考材料

WP REST API 官方文件
[https://www.sofplant.com/blog/tech/wp-rest-api-tips/](https://www.sofplant.com/blog/tech/wp-rest-api-tips/)

从 WordPress 4.7 开始，我们使用成为标准的[WP REST API]建立自己的端点，并在跨域中获取和显示数据。

Wordpress 桴萸询撰. jp

假设接收和显示数据的站点是 fugafuga.jp。

hogehoge.jp

请描述一下 functions.php。

```
<?php
//wordpressに用意されているアクションフックで独自 APIを作成します。

add_action('rest_api_init', function() {
    register_rest_route( 'wp/v2', '/org_api', array(
        'methods' => 'GET',
        'callback' => 'org_api',
    ));
});
function org_api( ) {

    $contents = array();//return 用の配列を準備
    $myQuery = new WP_Query();//取得したいデータを設定
    $param = array(
        'post_type' => 'blog',
        'posts_per_page' => 3,
        'order' => 'DESC'
    );
    $myQuery->query($param);
     if($myQuery->have_posts()):
         while ( $myQuery->have_posts() ) : $myQuery->the_post();
                $ID = get_the_ID();
                $title = get_the_title();
                array_push($contents, array(
                    "title" => $title,
                    "id" => $ID
                ));
         endwhile;
     endif;
     return $contents;// WP REST APIを利用するときはjsonで返ってくる様に設定されているので、json_encodeは必要ありません。
 } 
```

请访问[http://hogehoge.jp/wp-json/wp/v2/org_api](http://hogehoge.jp/wp-json/wp/v2/org_api)并检查 json 是否会回来。

如果在这一点上出现错误，php 端可能会出现语法错误。

接收数据。
在跨域使用时，指定 jsonp 为数据类型。
fugafuga.jp

```
<p id="view_area"></p> 
```

```
<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.2.4/jquery.min.js"></script>
<script>
    $(function(){
            $.ajax({
            type: 'GET',
            url: 'http://hogehoge.jp/wp-json/wp/v2/org_api?_jsonp=callback',//コールバック関数名を指定する。 
            dataType: 'jsonp',
            jsonpCallback: 'callback',//コールバック関数名を指定する。
            success: function (jsonp) {
                    $(jsonp).each(function(index,el){
                    let id = (jsonp[index]['id']);
                    let title = (jsonp[index]['title']);
                        $('#view_area').append(
                        '<p>' + id + '</p>'
                         + '<p>' + title  + '</p>'
                        );
                });
            }
        });
    });
</script> 
```

我想数据显示是否可以用上面的。
加工等。当数据不能被采集时被省略。
通过使用您自己的端点，使用标准 API(自定义发布等)很难获得的数据。)
我认为，通过向请求 url 传递一个喜爱的参数并在 php 端使用它，可以获得灵活的数据。

```
data : {
    'param' : 'key_word'
} 
```

```
 <?php
//wordpressに用意されているアクションフックで独自 APIを取得します。

add_action('rest_api_init', function() {
    register_rest_route( 'wp/v2', '/org_api', array(
        'methods' => 'GET',
        'callback' => 'org_api',
    ));
});
function org_api( ) {
$param = $_GET['param'];
} 
```

谢谢！