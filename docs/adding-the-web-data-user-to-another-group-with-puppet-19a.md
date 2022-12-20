# 使用 puppet 将 web 数据用户添加到另一个组

> 原文：<https://dev.to/c33s/adding-the-web-data-user-to-another-group-with-puppet-19a>

这篇博文主要是给自己的一个笔记，以备日后参考。

`profile::nginx`

```
 ...
  # the user is not created here. lets say the user is prepared here.
  @user { 'www-data':
    # groups     => ['somegroup'],
    gid         => '33',
    membership  => minimum,
  }
  ... 
```

`profile::other_profile_which_needs_to_add_the_group`

```
 # here the user is created aka realized but with the extra group `mynewgroup` added.
  User <| title == 'www-data' |> { groups +> "mynewgroup" } 
```

`@@`表示资源被导出。又见[https://puppet.com/docs/puppet/5.3/lang_exported.html](https://puppet.com/docs/puppet/5.3/lang_exported.html)

```
@@user { 'www-data':
    ...
} 
```

`@`表示资源是虚拟的。又见[https://puppet.com/docs/puppet/5.3/lang_virtual.html](https://puppet.com/docs/puppet/5.3/lang_virtual.html)

```
@user { 'www-data':
    ...
} 
```

延伸阅读:

*   [https://server fault . com/questions/416254/add-an-existing-a-user-to-a-group with puppet](https://serverfault.com/questions/416254/adding-an-existing-user-to-a-group-with-puppet)
*   [https://tickets.puppetlabs.com/browse/PUP-7541](https://tickets.puppetlabs.com/browse/PUP-7541)
*   [https://blog . Scott Lowe . org/2013/02/08/modificing-virtual-user-resources-in-puppet/](https://blog.scottlowe.org/2013/02/08/modifying-virtual-user-resources-in-puppet/)
*   [https://ttboj . WordPress . com/2013/05/14/overriding-attributes-of-collected-exported-resources/](https://ttboj.wordpress.com/2013/05/14/overriding-attributes-of-collected-exported-resources/)
*   [https://server fault . com/questions/707261/can-I-accumulate-a-array-of-values-in-puppet-from-a-collector](https://serverfault.com/questions/707261/can-i-accumulate-an-array-of-values-in-puppet-from-a-collector)

*   封面图片由加比·斯坦恩拍摄，又名斯坦恩[https://pixabay.com/en/stage-boards-wood-theater-233085/](https://pixabay.com/en/stage-boards-wood-theater-233085/)