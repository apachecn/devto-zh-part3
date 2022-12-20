# 已解决:在 django 迁移中找不到模块

> 原文：<https://dev.to/highcenburg/modules-couldn-t-found-on-django-l6o>

当我在我的 django 项目中进行移植时，我遇到了两个模块没有找到；脆皮 _ 形式与存储。

我的 pip 清单是:

```
Package             Version
------------------- --------
boto3               1.9.159
botocore            1.12.159
certifi             2019.3.9
chardet             3.0.4
dj-database-url     0.5.0
Django              2.2.1
django-crispy-forms 1.7.2
django-heroku       0.3.1
django-storages     1.7.1
docutils            0.14
gunicorn            19.9.0
idna                2.8
jmespath            0.9.4
Markdown            3.1.1
martor              1.4.0
Pillow              6.0.0
pip                 19.1.1
psycopg2            2.8.2
python-dateutil     2.8.0
pytz                2019.1
requests            2.22.0
s3transfer          0.2.0
setuptools          41.0.1
six                 1.12.0
sqlparse            0.3.0
urllib3             1.25.3
wheel               0.33.4
whitenoise          4.1.2

```

我的设置中已安装的应用程序有

```
INSTALLED_APPS = [
     ....
    'crispy_forms',
    'storages',
]
```

当我试图在 heroku 的 bash 上迁移时，我得到:

```
psycopg2.errors.DuplicateTable: relation "blog_about" already exists
```

有人熟悉这个吗？

### 解:

在 django 仓库重新安装了 crispy _ forms