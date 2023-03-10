# 在 WordPress 中注册一些自定义设置

> 原文：<https://dev.to/19h47/register-some-custom-settings-in-wordpress-32pf>

最近我玩了很多关于 WPGraphQL 的插件，这是一个为 T4 WordPress T5 启用 T2 GraphQL API 的插件。我需要在我的应用程序中检索社交网络的链接。

从历史上看，我会用[定制器 API](https://codex.wordpress.org/Theme_Customization_API) 来做这件事，但是 **WPGraphQL** 目前没有公开这个数据。

所以，多亏了 **WPGraphQL** 文档，我想到了另一个主意:使用[设置 API](https://codex.wordpress.org/Settings_API) 。WPGraphQL 公开了 WPGraphQL 模式中的所有核心设置。

所以我认为 documented 可能对任何人都有用，即使是 WPGraphQL 生态系统之外的人。

### 设置 API

三个功能对于实现您的目标至关重要:

*   [add_settings_section](https://developer.wordpress.org/reference/functions/add_settings_section/) ，用于为管理页面定义新的设置部分；
*   [add_settings_field](https://developer.wordpress.org/reference/functions/add_settings_field/) ，用于定义一个设置字段；
*   [register_setting](https://developer.wordpress.org/reference/functions/register_setting/) ，登记设置和数据。

### 设置类

我们将编写一个名为`Settings` :
的 PHP 类

```
class Settings {
    /**
     * The theme name
     */
    protected $theme_name;

    /**
     * The theme version
     */
    protected $theme_version;

    /**
     * The constructor
     */
    public function __construct( $theme_name, $theme_version ) {
        $this->theme_name    = $theme_name;
        $this->theme_version = $theme_version;
    }
} 
```

这个类将在我们的主题(或插件)的`functions.php`中被实例化。

### 设置 API 初始化方法

所以我们要为我们的`Settings`类写一个方法来添加设置:

```
public function settings_api_init() {
    add_settings_section(
        'socials',
        'Socials',
        // In class context pass an array with $this and the method name
        // to retrieve callback function.
        array( $this, 'socials_callback_function' ),
        // Our Socials setting will be set under the General tab.
        'general'
    );

    add_settings_field(
        'twitter',
        'Twitter',
        array( $this, 'setting_callback_function' ),
        'general',
        // Display this setting under our newly declared section right
        // above.
        'socials',
        // Extra arguments used in callback function.
        array(
            'name'  => 'twitter',
            'label' => 'Twitter',
        )
    );
} 
```

### 回调方法

`add_settings_section`和`add_settings_field`取一个回调函数，`socials_callback_function`和`setting_callback_function`分别加入我们的类:

```
public function socials_callback_function() {
    echo '<p>Socials urls</p>';
}

public function setting_callback_function( $args ) {
    // Ugly, I know 😔.
    echo '<input name="' . esc_attr( $args['name'] ) . '" type="text" value="' . esc_attr( get_option( $args['name'] ) ) . '" class="regular-text code" placeholder="' . esc_attr( $args['label'] ) . ' URL" />'; 
    echo ' ' . esc_attr( $args['label'] ) . ' URL';
} 
```

### 寄存器设置方法

接下来，我们要注册这个设置，这样 WordPress 就可以处理这个:

```
public function register_settings() {
    $args = array(
        'type'              => 'string',
        'sanitize_callback' => 'sanitize_text_field',
        'default'           => null,
        // Extra argument for WPGraphQL.
        'show_in_graphql'   => true,
        'show_in_rest'      => true,
     );

     register_setting( 'general', 'twitter', $args );
} 
```

### 构造函数

最后，在我们的构造函数中，我们可以在适当的钩子:
中将这个方法附加到类上

```
 public function __construct( $theme_name, $theme_version ) {
    $this->theme_name    = $theme_name;
    $this->theme_version = $theme_version;

    add_action( 'admin_init', array( $this, 'settings_api_init' ) );
    // We hook into init and not admin_init to make front aware of this
    // settings.
    add_action( 'init', array( $this, 'register_settings' ) );
} 
```

现在，我们可以在`functions.php`中实例化我们的`Settings`类，并在通用设置页面中检索我们的设置。

[![WordPress custom settings](img/ed0c882e7684f3e6403737a04225dcf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hsXDa1vU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yx22sbpqtxuoj0xcr8ul.jpg)

酷吧？而且没有任何插件。😎

### 结论

当然，我们可以更进一步，使用其他类型输入，显示页面选择，显示一些复选框。我们可以为所欲为！

如果你看到任何错别字或有任何问题，请随时纠正我。

–
<small>要检索整个类，请遵循此[链接](https://gist.github.com/19h47/2e0e03a3caede1c258d95a5d7f7e16e3)</small>

–

#### 奖金 WPGraphQL

现在我们的设置已经注册，在`generalSettings`条目下，我们将找到我们的参数:

```
 generalSettings {
    twitter
} 
```