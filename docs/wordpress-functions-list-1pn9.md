# WordPress 功能列表

> 原文：<https://dev.to/alicannklc/wordpress-functions-list-1pn9>

创建添加动作

```
/**
 * Create Add_action
 */

add_action( $hook, $function_to_add, $priority, $accepted_args ); 
```

创建 Add_action_google_font

```
/**
 * Create Add_action_google_font
 */
function add_google_font() {

    wp_enqueue_style( 'google_font', 'http://fonts.googleapis.com/css?family=Pacifico' );

}
add_action( 'wp_enqueue_scripts', 'add_google_font' ); 
```

创建添加过滤器

```
/**
 * Create Add_filter
 */
add_filter( $tag, $function_to_add, $priority, $accepted_args ); 
```

创建添加管理菜单

```
/**
 * Create Add_admin_menu
 */
add_action( 'admin_menu' , 'my_plugin_page' );

function my_plugin_page() {

    add_options_page( 'My Page', 'My Page', 'manage_options', 'my_plugin', 'my_plugin_page' );

}

add_action( 'admin_menu', 'register_my_custom_menu_page' );

function register_my_custom_menu_page() {
    add_menu_page( 'custom menu title', 'custom menu', 'manage_options', 'myplugin/myplugin-admin.php', '', 'dashicons-admin-site', 6 );
} 
```

[核心菜单项的位置](http://codex.wordpress.org/Function_Reference/add_menu_page)

[大栅栏](https://developer.wordpress.org/resource/dashicons/)

创建添加应用过滤器

```
/**
 * Create Add_apply_filters
 */
// First create a function and hook it into new example hook
function example_callback( $string ) {
    $new_value = $string . " NEW!";
    return $new_value;
}
add_filter( 'example_filter', 'example_callback' );

// Then use apply_filters in your code when you want the filter to run
echo $value = apply_filters( 'example_filter', 'Default value' );

// From http://wpseek.com/function/apply_filters/ 
```

创建 Add_body_class

```
/**
 * Create Add_body_class
 */
function custom_body_classes( $classes ) {

    if ( 'post' == get_post_type() ) {
        $classes[] = "custom-class";
    }   

    return $classes;
}

add_filter( 'body_class', 'custom_body_classes' ); 
```

创建 Add_init

```
/**
 * Create Add_body_class
 */
function my_init_function() {

    echo current_filter(); // 'init'
}
add_action( 'init', 'my_init_function' ); 
```

创建 Add_do_action

```
/**
 * Create Add_do_action
 */
    do_action( $tag, $arg );

    do_action( $tag, $arg_a, $arg_b, $etc );

    do_action_ref_array( $tag, $args ); 
```

创建添加操作挂钩

```
/**
 * Create Add_do_action_hooks
 */
    function custom_footer() {
        do_action('my_footer');
    }

    function custom_footer_text() {
        echo "Custom footer text";
    }
    add_action( 'my_footer', 'custom_footer_text' ); 
```

定义你自己的 WordPress 动作钩子

创建添加过滤摘录长度

```
/**
 * Create add_filter_excerpt_length
 */
 add_filter( 'excerpt_length', 'excerpt_length_example' );

function excerpt_length_example( $words ) {
    return 15;
} 
```

创建散列 _ 动作

```
/**
 * Create has_action
 */
if ( has_action( 'init', 'custom_plugin_code' ) ) {
    remove_action( 'init', 'custom_plugin_code' );
    add_action( 'init', 'my_content_code' );
} 
```

创建散列 _ 过滤器

```
/**
 * Create has_filter
 */
if ( has_filter( 'custom_plugin_filter' ) ) {
    add_filter( 'custom_plugin_filter', 'my_custom_code' );
}

if ( has_filter( 'the_content', 'custom_plugin_function' ) ) {
    remove_filter( 'the_content', 'custom_plugin_function' );
    add_filter( 'the_content', 'my_content_code' );
} 
```

创建钩子 _ 调试 _ 函数

```
/**
 * Create has_filter
 */
function list_hooks( $filter = false ){
    global $wp_filter;

    $hooks = $wp_filter;
    ksort( $hooks );

    foreach( $hooks as $tag => $hook )
        if ( false === $filter || false !== strpos( $tag, $filter ) )
            dump_hook($tag, $hook);
}

function list_live_hooks( $hook = false ) {
    if ( false === $hook )
        $hook = 'all';

    add_action( $hook, 'list_hook_details', -1 );
}

function list_hook_details( $input = NULL ) {
    global $wp_filter;

    $tag = current_filter();
    if( isset( $wp_filter[$tag] ) )
        dump_hook( $tag, $wp_filter[$tag] );

    return $input;
}

function dump_hook( $tag, $hook ) {
    ksort($hook);

    echo "<pre>&gt;&gt;&gt;&gt;&gt;\t<strong>$tag</strong><br />";

    foreach( $hook as $priority => $functions ) {

        echo $priority;

        foreach( $functions as $function )
            if( $function['function'] != 'list_hook_details' ) {

                echo "\t";

                if( is_string( $function['function'] ) )
                    echo $function['function'];

                elseif( is_string( $function['function'][0] ) )
                    echo $function['function'][0] . ' -> ' . $function['function'][1];

                elseif( is_object( $function['function'][0] ) )
                    echo "(object) " . get_class( $function['function'][0] ) . ' -> ' . $function['function'][1];

                else
                    print_r($function);

                echo ' (' . $function['accepted_args'] . ') <br />';
            }
    }

    echo '</pre>';
} 
```

创建添加内容过滤器

```
/**
 * Create add_filter_the_content
 */
function only_run_in_main_loop( $content ) {
    if ( is_main_query() ) {

    }

}
add_filter( 'the_content', 'only_run_in_main_loop' ); 
```

创建 list_hooked_functions

```
/**
 * Create list_hooked_functions
 */
list_hooked_functions();

function list_hooked_functions($tag=false){
    global $wp_filter;
    if ($tag) {
        $hook[$tag]=$wp_filter[$tag];
        if (!is_array($hook[$tag])) {
            trigger_error("Nothing found for '$tag' hook", E_USER_WARNING);
            return;
        }
    }
    else {
        $hook=$wp_filter;
        ksort($hook);
    }
    echo '<pre>';
    foreach($hook as $tag => $priority){
        echo "<br />&gt;&gt;&gt;&gt;&gt;\t<strong>$tag</strong><br />";
        ksort($priority);
        foreach($priority as $priority => $function){
            echo $priority;
            foreach($function as $name => $properties) echo "\t$name<br />";
        }
    }
    echo '</pre>';
    return;
} 
```

创建 manage_posts_columns

```
/**
 * Create manage_posts_columns
 */
function manage_posts_columns_example( $columns ) {

    unset( $columns['author'] );    
    unset( $columns['categories'] );
    unset( $columns['tags'] );
    unset( $columns['comments'] );
    return $columns;

}

add_filter( 'manage_posts_columns', 'manage_posts_columns_example' ); 
```

创建注册 _ 菜单

```
/**
 * Create register_menu
 */
function register_my_menus() {
  register_nav_menus(
    array(
      'footer_menu' => __( 'Footer Menu', 'mytheme' )      
    )
  );
}
add_action( 'init', 'register_my_menus' ); 
```

创建移除 _ 动作

```
/**
 * Create remove_action
 */
remove_action( $tag, $function_to_remove, $priority ); 
```

创建移除 _ 动作

```
/**
 * Create remove_action
 */
remove_action( 'wp_enqueue_scripts', 'add_google_font' ); 
```

创建添加 _ 过滤 _ 批量 _ 操作-编辑-发布

```
/**
 * Create add_filter_bulk_actions-edit-post
 */
function my_custom_bulk_actions($actions){
    var_export( $actions ); 
    unset( $actions['delete'] );
    return $actions;
}
add_filter('bulk_actions-edit-post','__return_empty_array'); 
```

创建移除 _ 过滤器

```
/**
 * Create remove_filter
 */
remove_filter( $tag, $function_to_remove, $priority ); 
```

创建 widgets_init

```
/**
 * Create widgets_init
 */
function create_my_widget() {

    register_sidebar(array(
        'name' => __( 'My Sidebar', 'mytheme' ),    
        'id' => 'my_sidebar',
        'description' => __( 'The one and only', 'mytheme' ),
    ));

}

add_action( 'widgets_init', 'create_my_widget' ); 
```

创建 wp_enqueue_scripts 和 wp_enqueue_style

```
/**
 * Create wp_enqueue_scripts and wp_enqueue_style
 */
function theme_styles() {

    wp_enqueue_style( 'bootstrap_css', get_template_directory_uri() . '/css/bootstrap.min.css' );
    wp_enqueue_style( 'main_css', get_template_directory_uri() . '/style.css' );

}
add_action( 'wp_enqueue_scripts', 'theme_styles' );

function theme_js() {

    wp_enqueue_script( 'bootstrap_js', get_template_directory_uri() . '/js/bootstrap.min.js', array('jquery'), '', true );
    wp_enqueue_script( 'theme_js', get_template_directory_uri() . '/js/theme.js', array('jquery', 'bootstrap_js'), '', true );

}
add_action( 'wp_enqueue_scripts', 'theme_js' ); 
```

创建添加过滤登录重定向

```
/**
 * Create add_filter_login_redirect
 */
add_filter( 'login_redirect', 'login_redirect_example', 10, 3 );

function login_redirect_example( $redirect_to, $request, $user ) {
    global $user;
    if ( isset( $user->roles ) && is_array( $user->roles ) ) {
        if ( in_array( 'subscriber', $user->roles ) ) {
            return home_url();
        } else {
            return $redirect_to;
            }
    }
    return;
} 
```

创建添加过滤器 wp 标题

```
/**
 * Create add_filter_wp_title
 */

function custom_wp_title( $title, $sep ) {
    global $page;

        // Add the site name.

    $title .= get_bloginfo( 'name' );

        // Add the site description for the home/front page.

    $site_description = get_bloginfo( 'description', 'display' );
    if ( $site_description && ( is_home() || is_front_page() ) ) {
        $title = "$title  $sep  $site_description";
    } 

    return $title;

} 
add_filter( 'wp_title', 'custom_wp_title', 20, 2 ); 
```

[过滤-可湿性粉剂-标题](https://tommcfarlin.com/filter-wp-title/)

[回购](https://github.com/Alicannklc/wordpress-functions-list)