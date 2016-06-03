{% raw %}
##站点地图

此篇来讲讲如何生成博客的站点地图。

django 自带了一个网站地图框架：[https://docs.djangoproject.com/en/1.8/ref/contrib/sitemaps/](https://docs.djangoproject.com/en/1.8/ref/contrib/sitemaps/)

接下来就是介绍其用法了（其中最简单的一种）。


###1. 安装 sitemap app

* 编辑 `settings.py` 文件，在 `INSTALLED_APPS` 中添加 `'django.contrib.sitemaps',`
* `TEMPLATES` 中必须有如下两项（默认已经包含，若你没有修改 `TEMPLATES`，则不必理会此条）

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'APP_DIRS': True,
    },
]
```

###2. 添加链接地址（urls.py）

编辑 `blog/urls.py`，添加：

```python
from django.contrib.sitemaps.views import sitemap

from views import BlogSitemap

urlpatterns = [
    #sitemap
    url(r'^sitemap\.xml$', sitemap, {'sitemaps': {'blog': BlogSitemap}},name='django.contrib.sitemaps.views.sitemap')
]
```

###3. 实现视图类（views.py）

编辑 `blog/views.py`，添加 `BlogSitemap` 类：

```python
from django.contrib.sitemaps import Sitemap

class BlogSitemap(Sitemap):
    changefreq = "daily"
    priority = 0.5

    def items(self):
        return Article.objects.all()

    def lastmod(self, obj):
        return obj.updated

    def location(self, obj):
        return obj.url
```

###4. 访问 sitemap.xml

访问：http://192.168.1.221:8000**/blog/sitemap.xml**

可点击查看本博客的 `sitemap.xml`：[http://10ns.top/blog/sitemap.xml](http://10ns.top/blog/sitemap.xml)
{% endraw %}
