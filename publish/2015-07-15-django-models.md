{% raw %}
##Models

模型与数据库息息相关，一个模型对应一个数据库表（所以，在设计 models 时，可想象成是在设计相应的数据库表）。

模型的特点：

* 每一个模型都是一个 Python 类，且继承至 `django.db.models.Model` 类
* 模型中每一个属性对应数据库表中的一个字段


###属性类型
每个属性的定义均类似如下定义方式：
```python
title = models.CharField(max_length=50)
```

上述代码，定义了一个名为 `title` 的属性，且此属性为 string 类型（`CharField`）。

查看更多类型或详细内容请[点击这里](https://docs.djangoproject.com/en/1.8/ref/models/fields/#field-types)

###更新 blog/models.py

####1. 设计 blog/models.py
在之前，咱们创建了一个名为 blog 的 app. 在 blog/ 目录下生成了 models.py 文件，现在咱们来设计它：
```python
from django.db import models

class Article(models.Model):
    url = models.URLField()	
    title = models.CharField(max_length=50)
    title_zh = models.CharField(max_length=50)
    author = models.CharField(max_length=30)
    content_md = models.TextField()
    content_html = models.TextField()
    tags = models.CharField(max_length=30)
    views = models.IntegerField()
    created = models.DateTimeField()
    updated = models.DateTimeField()
```
稍微解释一下各个属性的含义与作用：

* url，文章的链接地址
* title，文章的标题（英文，即为链接中显示的英文名称）
* title_zh，文章的中文标题（此标题是从文章内容中读取的，取 `content_html` 中第一个 `<h(\d)>` 标签的内容）
* author，文章作者
* content_md，文章的内容，因为是使用 `markdown` 格式书写的，所以加了后缀 `md`
* content_html，文章的内容，html 格式，存储是为了不需要每次都去将 md 解析成 html
* tags，文章的标签
* views，文章被阅读的次数
* created，文章发表的日期时间
* updated，文章被更新的日期时间

####2. 生成数据库表
模型编辑好之后，需要生成数据库表（在当你改变了 `models.py` 内容时，都需要重新生成数据库表）：
```shell
$ python manage.py makemigrations
$ python manage.py migrate
```
运行结果如下：

```shell
(env_django)shengan@ubuntu:~/mysite$ python manage.py makemigrations
Migrations for 'blog':
  0001_initial.py:
    - Create model Article
```

```shell
(env_django)shengan@ubuntu:~/mysite$ python manage.py migrate
Operations to perform:
  Synchronize unmigrated apps: staticfiles, messages
  Apply all migrations: admin, blog, contenttypes, auth, sessions
Synchronizing apps without migrations:
  Creating tables...
    Running deferred SQL...
  Installing custom SQL...
Running migrations:
  ...##（省略）
```

####3. 查看数据库表
进入 mysql 数据库看看生成的表：

```mysql
mysql> use xblog;
```

```mysql
mysql> show tables;
+----------------------------+
| Tables_in_xblog            |
+----------------------------+
| auth_group                 |
| auth_group_permissions     |
| auth_permission            |
| auth_user                  |
| auth_user_groups           |
| auth_user_user_permissions |
| blog_article               |
| django_admin_log           |
| django_content_type        |
| django_migrations          |
| django_session             |
+----------------------------+
11 rows in set (0.03 sec)
```

```mysql
mysql> desc blog_article;
+--------------+--------------+------+-----+---------+----------------+
| Field        | Type         | Null | Key | Default | Extra          |
+--------------+--------------+------+-----+---------+----------------+
| id           | int(11)      | NO   | PRI | NULL    | auto_increment |
| url          | varchar(200) | NO   |     | NULL    |                |
| title        | varchar(50)  | NO   |     | NULL    |                |
| title_zh     | varchar(50)  | NO   |     | NULL    |                |
| author       | varchar(30)  | NO   |     | NULL    |                |
| content_md   | longtext     | NO   |     | NULL    |                |
| content_html | longtext     | NO   |     | NULL    |                |
| tags         | varchar(30)  | NO   |     | NULL    |                |
| views        | int(11)      | NO   |     | NULL    |                |
| created      | datetime     | NO   |     | NULL    |                |
| updated      | datetime     | NO   |     | NULL    |                |
+--------------+--------------+------+-----+---------+----------------+
11 rows in set (0.07 sec)
```
{% endraw %}
