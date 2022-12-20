# 使用 Ansible 将 MySQL root 用户从 auth_socket 改为密码认证

> 原文：<https://dev.to/oscherler/changing-the-mysql-root-user-from-authsocket-to-password-authentication-using-ansible-3l5m>

MySQL 5.7 引入了一个变化，在安装时，root 用户使用`auth_socket`认证插件进行配置。这意味着，如果您以 root 用户身份从 shell 启动 MySQL 客户端(通过套接字连接，正如插件的名称所暗示的那样)，那么您将根据您在系统上的用户 ID 自动通过身份验证，无需密码。这是一件好事，因为它开箱即用，而且不需要设置 root 密码。

但是有时您需要在根用户上设置一个老式的密码。在我的例子中，我想用一台最新的 Ubuntu 开发机器来替换它，并且我想为它提供 Ansible。我们有很多正在开发的 PHP 项目在旧机器上运行，它们都使用 root 用户连接到 MySQL，因此创建一个新用户来处理新插件设置并不太实际。不幸的是，在撰写本文时，Ansible `mysql_user`模块不支持身份验证插件，如果您试图在 root 用户上设置密码，将会失败。

更新 Ansible `mysql_user`模块以支持新的插件似乎是正确的事情，但结果比我预期的要复杂。认证插件在 MariaDB 中引入，然后进入 MySQL，插件和列名，以及一些命令的语法[在风格和版本之间有很大的变化](https://github.com/oscherler/ansible-mysql-fix#versions-tested)。因此，即使有 Docker 的帮助，也很难测试你的改变。

我终于找到了一个相当不错的解决方法，使用了未改变的 Ansible `mysql_user`模块和一些 SQL 命令，我想在这里分享一下。

**免责声明:**这种方法还没有经过广泛的测试，虽然对作者有效，但很可能是不安全的。仅在开发虚拟机或其他非关键情况下使用它。

这个概念是首先创建一个新的`auth_socket`用户供 Ansible 使用，如果使用的是`auth_socket`插件，删除并重新创建一个带密码的 root 用户。

这个技巧的基础是一个任务文件，我们将为所有想要修改的用户导入这个文件:

```
# mysql_setup_user.yml
# 
# Setup a MySQL user, supporting both the mysql_native_password
# and auth_socket plugins.
# 
# Variables used:
# - login_user: auth_socket user to invoke the MySQL client with (default: root)
# - username: user to configure
# - plugin: authentication plugin for username (default: mysql_native_password)
# - password: password for username
# - priv: privileges to grant
# - host: host to grant privileges for

- name: Apply default values
  set_fact:
    actual_login_user: '{{  login_user  |  default("root")  }}'
    actual_plugin: '{{  plugin  |  default("mysql_native_password")  }}'
    actual_host: '{{  host  |  default("localhost")  }}'

# Check existence and authentication plugin of the user to configure
- name: 'Get  {{  username  }}@{{  actual_host  }}  user'
  shell: |
    mysql -u {{ actual_login_user }} -p'{{ login_password }}' -NBe 'SELECT plugin FROM mysql.user WHERE Host="{{ actual_host }}" AND User = "{{ username }}"'
  failed_when: no
  changed_when: no
  register: mysql_plugin_output

# User exists if the output of the previous task is not empty
# The auth plugin is the output of the previous task
- name: 'Store  {{  username  }}  user  info'
  set_fact:
    user_exists: '{{  mysql_plugin_output.stdout  !=  ""  }}'
    user_current_plugin: '{{  mysql_plugin_output.stdout  }}'

# If the requested user doesn’t exist and the plugin is mysql_native_password,
# create it using the mysql_user module
- name: 'Create  {{ username  }}  user  (if  plugin  ==  mysql_native_password)'
  mysql_user:
    login_user: '{{  actual_login_user  }}'
    login_password: '{{  login_password  }}'
    name: '{{ username  }}'
    host: '{{ actual_host  }}'
  when: 'actual_plugin  ==  "mysql_native_password"  and  not  user_exists'

# If the requested user doesn’t exist but the plugin is auth_socket,
# then we can’t create it using the mysql_user module, so we resort
# to a shell command
- name: 'Create  {{ username  }}  user  (if  plugin  ==  auth_socket)'
  shell: |
    mysql -u {{ actual_login_user }} -p'{{ login_password }}' -NBe 'CREATE USER "{{ username }}"@"{{ actual_host }}" IDENTIFIED WITH auth_socket'
  when: 'actual_plugin  ==  "auth_socket"  and  not  user_exists'

# Change the plugin if needed, providing the password if the new plugin
# is mysql_native_password.
# It only supports a clear text password, so use a vault.
- name: 'Alter  {{ username  }}  user  to  use  {{  actual_plugin  }}'
  shell: |
    mysql -u {{ actual_login_user }} -p'{{ login_password }}' -NBe 'ALTER USER "{{ username }}"@"{{ actual_host }}" IDENTIFIED WITH {{ actual_plugin }} {% if actual_plugin == "mysql_native_password" %}BY "{{ password }}"{% endif %}'
  when: 'user_current_plugin  !=  actual_plugin'

# If the plugin is mysql_native_password, update the user using Ansible mysql_user,
# to change its password.
- name: 'Update  {{ username  }}  user  (if  plugin  ==  mysql_native_password)'
  mysql_user:
    login_user: '{{  actual_login_user  }}'
    login_password: '{{  login_password  }}'
    name: '{{ username  }}'
    password: '{{ password  }}'
    host: '{{ actual_host  }}'
  when: 'actual_plugin  ==  "mysql_native_password"'

# Grant privileges
- name: 'Grant  privileges  to  user  {{ username  }}'
  mysql_user:
    login_user: '{{  actual_login_user  }}'
    login_password: '{{  login_password  }}'
    name: '{{ username  }}'
    host: '{{ actual_host  }}'
    priv: '{{  priv  }}' 
```

我们将分两次使用这个任务文件:一次是设置一个新的`auth_socket`用户，这样我们就可以更改 root 用户，另一次是实际更改它:

```
# mysql-root-playbook.yml

- name: Set MySQL root password
  hosts: all
  become: yes

  tasks:
    - name: Create user Ansible for MySQL administration with auth_socket
      user:
        name: 'ansible'
        state: present
        createhome: no

    - name: Setup ansible user
      include_tasks: mysql_setup_user.yml
      vars:
        username: ansible
        login_password: '{{  mysql_root_password  }}'
        plugin: auth_socket
        priv: '*.*:ALL,GRANT'

- name: Configure MySQL root user (using user ansible)
  hosts: all
  become: yes
  become_user: ansible

  tasks:
    - name: Setup root user
      include_tasks: mysql_setup_user.yml
      vars:
        login_user: ansible
        login_password: '{{  mysql_root_password  }}'
        username: root
        plugin: mysql_native_password
        password: '{{  mysql_root_password  }}'
        priv: '*.*:ALL,GRANT'
        host: '{{  item  }}'
      with_items:
        - 'localhost'
        - '127.0.0.1'
        - '%' 
```

现在，如果您设置变量`mysql_root_password`，并使用`ansible-playbook -i hosts.yml mysql-root-playbook.yml`运行上述剧本(用您的实际库存替换`hosts.yml`，将会发生以下情况:

第一个剧本，作为 root，将:

*   创建新的系统用户，`ansible`；
*   用插件`auth_socket`(不需要密码)和完全权限创建 MySQL 用户`ansible@localhost`，使用当前根用户`auth_socket`。

第二次播放时，用户`ansible`将:

*   看到`root@localhost`正在使用插件`auth_socket`；
*   将 root 用户改为使用`mysql_native_password`和所需的密码。

如果您再次运行剧本，它将会看到(以 root 用户的身份使用新设置的密码)用户`ansible`已按预期进行了配置，而 root 用户的`ansible`和`auth_socket`也按预期进行了配置。

这种方法有三个缺点:

*   挺啰嗦的；
*   它在命令行中以 root 用户的 MySQL 密码运行 shell 命令；
*   如果您随后更改 root 密码，它将会失败，因为它将无法使用密码作为 root 进行连接。

然而，这是为开发机器设计的一种变通方法，所以这并不重要。在生产机器上，您应该非常乐意使用`auth_socket`代替`root@localhost`。