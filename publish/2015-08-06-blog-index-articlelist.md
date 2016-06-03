{% raw %}
##博客首页--文章列表

此篇继上篇，在博客首页进行文章列表的显示。

上篇只是简单的一个示例而已，此篇将文章列表显示在首页上。

1. 首页指的是博客首页 **/blog/** ，首页分两栏，左边栏放文章列表，右边可根据个人喜好放一些标签、最新评论等
2. 此篇主要讲述左边栏的实现
3. 文章列表显示时进行分页，每页 10 篇文章，最多 10 页。

###1. 修改 url

一般要呈现列表，可使用 `django` 内置的 `ListView` 视图类，所以相应的 `blog/urls.py` 也要修改：

```python
url(r'^$', ArticleListView.as_view(), name='blog_index'),
```

###2. 修改 view

```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.db.models import F
from django.views.generic.list import ListView

from models import Article

class ArticleListView(ListView):
    template_name = 'blog_index.html'

    def get_queryset(self, **kwargs):
        object_list = Article.objects.all().order_by(F('created').desc())[:100]
        paginator = Paginator(object_list, 10)
        page = self.request.GET.get('page')
        try:
            object_list = paginator.page(page)
        except PageNotAnInteger:
            # If page is not an integer, deliver first page.
            object_list = paginator.page(1)
        except EmptyPage:
            # If page is out of range (e.g. 9999), deliver last page of results.
            object_list = paginator.page(paginator.num_pages)
        return object_list
```
讲解一下：

1. 根据文章发表时间来排序，[order_by(F('created').desc())](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#order-by)
2. 分页，每页 10 篇文章，[paginator = Paginator(object_list, 10)](https://docs.djangoproject.com/en/1.8/topics/pagination/#using-paginator-in-a-view)

###3. 修改 template

修改 `blog/templates/blog_index.html` 文件：

```jinja
{% extends 'blog_base.html' %}
{% load staticfiles %}

{% block content %}
<div class="container">
  <div class="row">
    <div class="col-md-8">
      {% for o in object_list %}
      <div class="article-item">
        <div class="article-info">
          <h3>{{ o.title_zh }}</h3>
          <p>阅读次数：{{ o.views }}，作者：{{ o.author }}，发布时间：{{ o.created|date:"Y/n/j" }}，文章标签：{{ o.tags }}</p>
        </div>
        <div class="summary">
          {% autoescape off %}
          <a href="{% url 'article_detail' o.title %}">{{ o.content_html|truncatechars_html:100 }}</a>
          {% endautoescape %}
        </div>
      </div>
      {% endfor %}
      <nav>
        <ul class="pager">
          <li class="current">{{ object_list.number  }} / {{ object_list.paginator.num_pages  }}</li>
          {% if object_list.has_previous %}
          <li class="previous"><a href="?page={{ object_list.previous_page_number  }}"><span aria-hidden="true">&larr;</span> 上一页</a></li>
          {% else %}
          <li class="previous disabled"><a href=""><span aria-hidden="true">&larr;</span> 上一页</a></li>
          {% endif %}
          {% if object_list.has_next %}
          <li class="next"><a href="?page={{ object_list.next_page_number  }}">下一页 <span aria-hidden="true">&rarr;</span></a></li>
          {% else %}
          <li class="next disabled"><a href="">下一页 <span aria-hidden="true">&rarr;</span></a></li>
          {% endif %}
        </ul>
      </nav>
    </div>
    <div class="col-md-4">
    </div>
  </div>
</div>
{% endblock %}
```

讲解一下：

```jinja
{% autoescape off %}
<a href="{% url 'article_detail' o.title %}">{{ o.content_html|truncatechars_html:100 }}</a>
{% endautoescape %}
```
1. `{% autoescape off %}{% endautoescape %}`，位于此之间的可直接输出 `html` 代码，因 `django` 模板中默认的是开启了 `html` 的安全模式（会将 `html` 的一些字符串进行转义，可看[这里](https://docs.djangoproject.com/en/1.8/ref/templates/builtins/#escape)）。
2. [truncatechars_html](https://docs.djangoproject.com/en/1.8/ref/templates/builtins/#truncatechars-html) 是 `django` 内置的过滤器，可将字符串截断指定的字符数，且截断点不会是 `html` 标签。

（分页的模板直接参考 [官网示例](https://docs.djangoproject.com/en/1.8/topics/pagination/) 和 [bootstrap 中的分页](http://v3.bootcss.com/components/#-6) 完成）

###4. 看看效果

直接访问博客首页（我这里以每页 1 篇文章作为示例，且相关的样式已经添加进 `blog.css` 文件中）：

![](/img/blog_index_articlelist_001.png)
{% endraw %}
