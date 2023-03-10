# 谷歌合作实验室中的 Ruby 内核。

> 原文：<https://dev.to/kojix2/ruby-kernel-in-google-colaboratory-32ni>

你好。我是红宝石迷。
我试着在[谷歌合作实验室](https://colab.research.google.com/)运行 Ruby。

[![image](img/1543258097102156a77a4cc352f737ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RCXmriD2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2g21nsval74ip86a6bqb.png)

打开[谷歌合作实验室](https://colab.research.google.com/)。
运行以下命令。

```
!apt-get install ruby-dev
!sudo apt install libtool libffi-dev ruby ruby-dev make
!sudo apt install libzmq3-dev libczmq-dev
!gem install ffi-rzmq rake
!gem install specific_install
!gem specific_install https://github.com/SciRuby/iruby
!iruby register 
```

Enter fullscreen mode Exit fullscreen mode

为数据科学安装 favoirite gems(可选)。

```
!gem install awesome_print
!gem specific_install https://github.com/SciRuby/daru
!gem specific_install https://github.com/SciRuby/daru-view
!gem install numo-narray
!gem install rumale 
```

Enter fullscreen mode Exit fullscreen mode

然后，使用`File -> Upload Notebook`将一个 Ruby ipynb 上传到 Colab。

```
{  "nbformat":  4,  "nbformat_minor":  0,  "metadata":  {  "colab":  {  "name":  "ruby.ipynb",  "version":  "0.3.2",  "provenance":  [],  "collapsed_sections":  []  },  "kernelspec":  {  "name":  "ruby",  "display_name":  "ruby 2.5.1"  }  },  "cells":  [  {  "metadata":  {  "id":  "BpxZ_k6Qv84W",  "colab_type":  "code",  "colab":  {}  },  "cell_type":  "code",  "source":  [  "puts \"hello world\""  ],  "execution_count":  0,  "outputs":  []  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以运行你的 ruby 脚本了！

[![image](img/7ea85f5fa8fbdeaeb62af2d131ed50e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x-FRdEqo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ylg4atkmhucvjiu9s8co.png)

祝你愉快！

检查 ruby 内核是否在 jupyter (Python 笔记本)

```
jupyter kernelspec list 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3a47d6a378784dd6a276adedd82a6623.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0nxu5uzy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fbbp4d74h167blnf8snd.png)