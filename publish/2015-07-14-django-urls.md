{% raw %}
##urls

###url 函数介绍
url 函数有 5 个参数：
```python
url(regex, view, kwargs=None, name=None, prefix='')

regex           ## 正则表达式，用来匹配 url 地址（必须）
view            ## 此 url 对应的视图函数（必须）
kwargs          ## 附加参数（字典类型），传递给视图函数（可选）
name            ## 此 url 的名称，在模板中用的比较多，且非常有用（可选）
prefix          ## 前缀，一般不用（可选）
```

正则表达式不熟悉也没有关系，django 中用到的不多。

若想了解 python 正则表达式，可参照这篇文章：[http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)

（文章末尾会贴出他那张经典的图片）


###一般开发中的做法
在创建 project 的时候，在 mysite 目录中生成了 urls.py 文件，此文件为整个项目 url 分发的入口。

在[之前](2015-07-13-first-django-app.html)，我们添加 url 时，仅仅是简单的将 blog 的 url 加入了其中；然而，在一般情况下，我们都会单独为 blog 新建一个 urls.py 文件。
```shell
shengan@ubuntu:~/mysite/mysite$ vim urls.py
```
```python
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),

    url(r'^blog/', 'blog.views.blog_index', name='blog_index'),
]
```
我们要更改为：
```python
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),

	# blog
    url(r'^blog/', include('blog.urls')),
]
```

然后去 blog 目录，新建 urls.py 文件：
```shell
shengan@ubuntu:~/mysite/blog$ vim urls.py
```

文件内容为：
```python
from django.conf.urls import url

urlpatterns = [
    url(r'^$', 'blog.views.blog_index', name='blog_index'),
]
```

再次使用浏览器进行访问，同样会看到 `Hello, world.`


###参考
* [https://docs.djangoproject.com/en/1.8/ref/urls/](https://docs.djangoproject.com/en/1.8/ref/urls/)
* [https://docs.djangoproject.com/en/1.8/intro/tutorial03/](https://docs.djangoproject.com/en/1.8/intro/tutorial03/)

###附

![](/img/django_urls_001.png)
{% endraw %}
