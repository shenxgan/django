{% raw %}
##访问文章

在文章发布之后，就应该进行查看效果了。流程和发布文章类似：

###1. urls.py

编辑 `blog/urls.py`，添加访问文章链接。

添加这一句：

```python
url(r'^article/(?P<title>\S+)$', ArticleDetailView.as_view(), name='article_detail'),
```

添加之后 `urls.py` 文件内容如下：

```python
from django.conf.urls import url

from views import ArticlePublishView, ArticleDetailView

urlpatterns = [
    url(r'^$', 'blog.views.blog_index', name='blog_index'),
    url(r'^article/publish$', ArticlePublishView.as_view(), name='article_publish'),
    url(r'^article/(?P<title>\S+)$', ArticleDetailView.as_view(), name='article_detail'),
]
```

1. `(?P<title>\S+)` 是 [django 中 url 的一种语法](https://docs.djangoproject.com/en/1.8/topics/http/urls/)，` \S+` 是正则表达式，表示匹配非空白字符一个或多个。这整句话的意思就是将 ` \S+` 匹配到的部分作为名为 `title` 的参数，此参数可传递给视图函数（可在 `blog/views.py` 中看到其用法）。
2. 可看到这次也是用的基于类的视图，将在 `views.py` 中实现 `ArticleDetailView` 视图类。


###2. views.py
在 `blog/views.py` 文件中添加如下代码：

```python
from django.http import Http404
from django.views.generic.detail import DetailView

from models import Article


class ArticleDetailView(DetailView):
    template_name = 'article_detail.html'

    def get_object(self, **kwargs):
        title = self.kwargs.get('title')
        try:
            article = Article.objects.get(title=title)
            article.views += 1
            article.save()
            article.tags = article.tags.split()
        except Article.DoesNotExist:
            raise Http404("Article does not exist")
        return article
```

1. 使用了 `django` 视图类 [DetailView](https://docs.djangoproject.com/en/1.8/ref/class-based-views/generic-display/#detailview)；
2. `get_object` 为 `DetailView` 类中的方法；
3. `Article.objects.get` 是 [django 中封装的对数据库的操作](https://docs.djangoproject.com/en/1.8/ref/models/querysets/)，此处用于获取指定文章内容。

###3. templates/article_detail.html

在 `blog/templates/` 目录下新建 `article_detail.html` 文件：

```html
{% extends 'blog_base.html' %}

{% block content %}
<div class="container">
  <div class="article">
    <div class="article-info">
      <p class="pull-right" title="发表日期"><span class="glyphicon glyphicon-calendar" aria-hidden="true"></span>{{ object.created|date:"Y/n/j G:i" }}</p>
      <p class="pull-right author" title="作者"><span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>{{ object.author }}</p>
      <p title="阅读次数"><span class="glyphicon glyphicon-eye-open" aria-hidden="true"></span>&times;{{ object.views }}</p>
    </div>
    <div class="article-tags">
      <span class="label label-primary"><span class="glyphicon glyphicon-tags" aria-hidden="true"></span> 文章标签：</span>
      {% for tag in object.tags %}
      <span class="label label-info"><span class="glyphicon glyphicon-tag" aria-hidden="true"></span> {{ tag }}</span>
      {% endfor %}
    </div>
    <div class="article-content">
      {% autoescape off %}
      {{ object.content_html }}
      {% endautoescape %}
      <p class="updated">---------- 全文完（最近更新于 {{ object.updated|date:"Y/n/j G:i" }}） ----------</p>
    </div>
  </div>
</div>
{% endblock %}
```

###4. 浏览器访问
还记得，咱们发布的第一篇文章吗（`test.html`）？现在已经可以访问啦。

输入地址：http://192.168.1.221:8000**/blog/article/test.html**

效果图：

![](/img/blog_article_detail_001.png)

样式什么的，咱在后续章节再讲。
{% endraw %}
