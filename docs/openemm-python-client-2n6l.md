# OpenEMM python 客户端

> 原文：<https://dev.to/pratham/openemm-python-client-2n6l>

### Py-OpenEMM

[**OpenEMM**](http://www.openemm.org) 是一个功能丰富的基于网络的企业应用程序，用于电子邮件营销、时事通讯和服务邮件。

**先决条件:**

*   Python 2.7 或更高版本
*   Python Suds 0.4 或更高版本

**安装:**

*   克隆回购

```
 git clone https://github.com/pratz/py-openemm.git 
```

Enter fullscreen mode Exit fullscreen mode

**配置:**

*   移动到克隆了回购的目录

```
 cd py-openemm/pyopenemm
  vi config.py 
```

Enter fullscreen mode Exit fullscreen mode

*   打开`config.py`文件，输入 openemm wsdl url、用户名和密码

```
 OPENEMM_URL = 'http://127.0.0.1:8080/cms_services/urn:agnitas-webservice?wsdl'
  WEBSERVICE_USER = 'test_user'
  WEBSERVICE_PASSWORD = 'test_123' 
```

Enter fullscreen mode Exit fullscreen mode

**用法:**

*   创建客户端连接

```
 from pyopenemm.connection.connect import create_connection
  client = create_connection()
  # connect to webservice , returned is suds client 
```

Enter fullscreen mode Exit fullscreen mode

*   获取 OpenEMM 客户端

```
 from pyopenemm.webservice.service import OpenEMM
  openemm = OpenEMM(client) 
```

Enter fullscreen mode Exit fullscreen mode

*   查找订户

```
 # get subcriber by email
  subscriber = openemm.find_subscriber(('email','test@gmail.com')) 
```

Enter fullscreen mode Exit fullscreen mode

*   获取订户详细信息

```
 subscriber_details = openemm.get_subscriber(subscriber) 
```

Enter fullscreen mode Exit fullscreen mode

*   添加新订户

```
 user_dict = {'email':'openemm@openemm.com','firstname':'openemmfirstname','lastname':'openemmlastname','gender':0}
  new_subscriber = openemm.add_subscriber(user_dict,True,'email',True) # add new subscriber to openemm 
  # Four parameters of add_subscriber() method
  # user_dict = Dictionary containing user information
  # double_check - If True, check if subscriber is already in database
  # key_column - column used for double_check
  # overwrite - If True, subscriber gets updated 
```

Enter fullscreen mode Exit fullscreen mode

[**随时贡献回来**](https://github.com/pratz/py-openemm)