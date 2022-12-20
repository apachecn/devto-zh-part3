# 在表单中使用 codeigniter 3 CAPTCHA 助手

> 原文：<https://dev.to/fadilxcoder/using-codeigniter-3-captcha-helper-in-forms-gjl>

嗨，

我将向您展示一个非常简单的方法来使用 codeigniter 3 的验证码助手时，提交一个表单在您的网络应用程序。

## 如此...准备，就位，开始！

为此，我们将使用 Welcome 控制器和 welcome_message 视图。所以第一步是编辑“**欢迎**控制器中的“**索引**方法”中的代码，并添加 3 个东西。

```
$this->load->library('form_validation');
$this->load->helper('captcha');
$this->load->library('session'); 
```

Enter fullscreen mode Exit fullscreen mode

1.  当用户在网页上提交表单时，我们加载表单验证库来验证数据。
2.  我们加载 captcha 助手，稍后我们将配置它。
3.  我们将使用会话库来存储验证码值。

现在我们必须配置验证码助手

```
 $vals = array(
'word'          => rand(1,999999),
'img_path'      => './assets/captcimg/',
'img_url'       => base_url('assets').'/captcimg/',
'font_path'     => base_url('assets').'/captcha/fonts/XYZ.ttf',
'img_width'     => '150',
'img_height'    => 30,
'word_length'   => 8,
'colors'        => array(
               'background'     => array(255, 255, 255),
               'border'         => array(255, 255, 255),
               'text'           => array(0, 0, 0),
               'grid'           => array(255, 75, 100)
            )
        ); 
```

Enter fullscreen mode Exit fullscreen mode

### 重要通知！

我之前忘了说，如果你的配置和上面一样，你必须设置你的项目文件结构如下。

```
your_ci_project
│
└───application
│
└───system
│
└───tests
│
└───index.php
│
└───.htaccess
│
└───assets
    │
    └───captcha
           │
           └───images
           │
           └───fonts
                 │
                 └───XYZ.ttf 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 [Codeigniter 用户指南](https://www.codeigniter.com/user_guide/helpers/captcha_helper.html)上找到更多细节。

配置完成后，调用创建验证码的函数，最后将验证码发送到" **welcome_message.php** "视图。

```
$data['captcha'] = create_captcha($vals);
$this->load->view('welcome_message',$data); 
```

Enter fullscreen mode Exit fullscreen mode

要在您的视图中看到验证码，只需添加这段代码。

```
echo $captcha['image'] 
```

Enter fullscreen mode Exit fullscreen mode

我希望到现在一切都好。

## 时间验证！

在这一部分，我们将编码的一部分，用户点击网页上的提交按钮，控制器将验证验证码。

假设我们的视图中有一个带有按钮的表单。

```
<?php echo $captcha['image'] ?>
<input type="text" name="captcha">
<input type="hidden" value="<?php echo $captcha['word'] ?>" name="code">
<button name="submit_contact">Send</button> 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面的代码中看到的，我有一个隐藏类型的输入标记，借此我存储了 captcha 字符串，当提交表单时，这些数据也被发送到控制器。

为了避免创建多个方法，我将使用 **index** one，但是将添加一个 **if/else** 条件语句来检查用户是否提交了表单。之后，我们将处理数据。

```
 if(isset($_POST['submit_contact'])):
   $this->session->set_userdata('captcha_answer',$this->input->post('code'));
   $this->form_validation->set_rules('captcha', 'Captcha', 'required|integer|callback_check_captcha');
   if($this->form_validation->run() == TRUE):
      $this->session->set_flashdata('positive','CAPTCHA VALIDATED SUCCESSFULLY');
      redirect(site_url());
   endif;
endif; 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面看到的，有一个对提交的验证码的回调，以检查它是否与图片中的相同。

```
 public function check_captcha($string)
{
   if($string != $this->session->userdata('captcha_answer')):
      $this->form_validation->set_message('check_captcha', 'captcha incorrect');
      return false;
   else:
      return true; 
   endif;
} 
```

Enter fullscreen mode Exit fullscreen mode

之后，如果你愿意，你可以破坏**验证码 _ 答案**的会话。

就是这样！