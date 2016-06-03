{% raw %}
##配置 MySQL 数据库

本文是讲述 django 中 mysql 数据库的配置，如若你仅仅是想继续学习下面的章节，而对数据库什么的没有要求，则此篇可略过。（django 中默认数据库为 sqlite，此篇不会影响你对后续章节的学习理解）

###django 支持的数据库
* PostgreSQL
* MySQL
* SQLite
* Oracle

说一句：MongoDB 是不支持的，不支持不代表不能用，只是如果在 Django 中使用 MongoDB 的话，需要你自己动手写相对应的扩展。

来看看 settings.py 中数据库的配置，可以看出默认的数据库即为 sqlite3
```python
# Database
# https://docs.djangoproject.com/en/1.8/ref/settings/#databases
 
DATABASES = { 
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }   
}
```

###修改数据库为 MySQL
参考：[https://docs.djangoproject.com/en/1.8/ref/databases/#mysql-notes](https://docs.djangoproject.com/en/1.8/ref/databases/#mysql-notes)

在 settings.py 文件中修改 `DATABASES` 字段如下：
```python
DATABASES = { 
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'xblog',	## 数据库名称
        'USER': 'root',
        'PASSWORD': 'password',	## 安装 mysql 数据库时，输入的 root 用户的密码
        'HOST': '127.0.0.1',
    }   
}
```

（若是你要在 py 文件中作中文注释，不要忘了在文件开头加上` #coding:utf-8`）

至此，MySQL 数据库的配置基本上是已经配置好了，但是你还不能够使用它，因为你系统中还没有安装 MySQL

###安装 MySQL

####1. Ubuntu 中安装 MySQL
```shell
$ sudo apt-get install mysql-server mysql-client
## 在过程中按照提示输入 mysql root 用户的密码，此密码将用于 settings.py 中。
```

安装完成之后，可于命令行中进入 mysql：
```shell
$ mysql -u root -p
Enter password: ## 输入 mysql root 用户密码，进入数据库
```
创建数据库 xblog
```mysql
mysql> create database xblog default charset utf8 collate utf8_general_ci;
Query OK, 1 row affected (0.20 sec)
```
####2. 安装 MySQLdb
```shell
(env_django)shengan@ubuntu:~/mysite$ pip install mysql-python
```
若是安装过程有问题，可安装下述软件包，然后再行安装 `MySQLdb`
```shell
sudo apt-get install python-setuptools
sudo apt-get install libmysqld-dev
sudo apt-get install libmysqlclient-dev
sudo apt-get install python-dev
```
进入 python 交互式命令行，输入 `import MySQLdb` 检测是否安装成功：
```python
>>> import MySQLdb	## 未报错即表示安装成功
```

至此，方可说 MySQL 的安装与配置均已完成。
{% endraw %}
