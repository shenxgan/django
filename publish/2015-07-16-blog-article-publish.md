{% raw %}
##发布文章

发布一篇文章，想想应该先有一个能写文字并发布的页面；而页面具有两个内容，一个是链接地址，一个是页面内容。

###1. 页面地址

首先我们需要为发布页面设计一个 url 地址，那就设为 `article/publish` 好了。

编辑 `blog/urls.py` 文件：

```python
from django.conf.urls import url

from views import ArticlePublishView

urlpatterns = [
    url(r'^$', 'blog.views.blog_index', name='blog_index'),
    url(r'^article/publish$', ArticlePublishView.as_view(), name='article_publish'),
]
```

可以看出，咱们这次的视图是[基于类的视图](https://docs.djangoproject.com/en/1.8/topics/class-based-views/)，而不是像 `blog_index` 那样的函数视图。具体用法可接着往下看。


###2. 视图
编辑 `blog/views.py` 文件：

```python
from django.views.generic.edit import FormView

from forms import ArticlePublishForm


class ArticlePublishView(FormView):
    template_name = 'article_publish.html'
    form_class = ArticlePublishForm
	success_url = '/blog/'

    def form_valid(self, form):
        form.save(self.request.user.username)
        return super(ArticlePublishView, self).form_valid(form)
```

可以看出，`ArticlePublishView` 类继承的是 `FormView`；使用的 `Form` 表单为 `ArticlePublishForm`；模板为 `article_publish.html` 。

* `FormView` 官网示例：[https://docs.djangoproject.com/en/1.8/ref/class-based-views/generic-editing/#formview](https://docs.djangoproject.com/en/1.8/ref/class-based-views/generic-editing/#formview)
* `FormView` 官网详细介绍：[https://docs.djangoproject.com/en/1.8/ref/class-based-views/flattened-index/#formview](https://docs.djangoproject.com/en/1.8/ref/class-based-views/flattened-index/#formview)

###3. 表单

在 `blog/` 目录下新建 `forms.py` 文件，文件内容如下：

```python
#!/usr/bin/env python
# coding=utf-8

import datetime
import re
import markdown

from django import forms

from models import Article


class ArticlePublishForm(forms.Form):
    title = forms.CharField(
        label=u'文章标题',
        max_length=50,
        widget=forms.TextInput(attrs={'class': '', 'placeholder': u'文章标题，记得在标题末尾添加".html"'}),
        )

    content = forms.CharField(
        label=u'内容',
        min_length=10,
        widget=forms.Textarea(),
        )

    tags = forms.CharField(
        label=u'标签',
        max_length=30,
        widget=forms.TextInput(attrs={'class': '', 'placeholder': u'文章标签，以空格进行分割'}),
        )

    def save(self, username):
        cd = self.cleaned_data
        title = cd['title']
        title_zh = title
        now = datetime.datetime.now()
        content_md = cd['content']
        content_html = markdown.markdown(cd['content'])
        re_title = '<h\d>(.+)</h\d>'
        data = content_html.split('\n')
        for line in data:
            title_info = re.findall(re_title, line)
            if title_info:
                title_zh = title_info[0]
                break
        url = '/article/%s' % (title)
        tags = cd['tags']
        article = Article(
            url=url,
            title=title,
            title_zh=title_zh,
            author=username,
            content_md=content_md,
            content_html=content_html,
            tags=tags,
            views=0,
            created=now,
            updated=now)
        article.save()
```

`Article` model 是在[前面章节](2015-07-15-django-models.html)讲到过的，这里继续沿用。

现在来说说 django 中的 form 表单。

html 中的 form 表单，大家应该不会陌生，各大网站上的登录页面中的登录框或页面上的评论/留言框就是一个 form 表单。基本上网页上需要人为输入并验证的都是 form 表单。

而 django 中的 form 表单可让 html 表单更加简单的实现，更加容易的验证；还有其他优点，详情请查看官网介绍：[https://docs.djangoproject.com/en/1.8/topics/forms/](https://docs.djangoproject.com/en/1.8/topics/forms/)

####save 函数

save 函数是在视图中进行调用的，在表单验证成功后执行。

save 函数将做如下几项工作：

1. 根据 `content` （文章内容，`Markdown` 格式），解析成 HTML 格式存入 `content_html` 中；
2. 从 `content_html` 中查找第一个 `<h(\d)>` 标签（也就是 `<h1>` `<h2>` ...），提取其内容作为中文标题；
3. 根据 `title` 生成 `url`（`title` 为英文标题，其实就是 `url`；在 `title` 后面加上 `.html` 可使这篇文章为伪静态页面，至于为什么要把其弄为伪静态，请自行上网搜索`伪静态`这个概念吧）；
4. 设置初始值：`views` （阅读次数）初始值为 0；`updated` （最近更新时间）初始值为 `created` （创建时间）；
5. 存入数据库。


###4. 模板

在 `blog/templates` 目录，创建 `article_publish.html` 模板文件：

```html
{% extends 'blog_base.html' %}

{% block extra_head %}
<link rel="stylesheet" href="//cdn.jsdelivr.net/editor/0.1.0/editor.css">
<script src="//cdn.jsdelivr.net/editor/0.1.0/editor.js"></script>
<script src="//cdn.jsdelivr.net/editor/0.1.0/marked.js"></script>
{% endblock %}

{% block content %}
<div class="container">
  <div class="publish center-block">
    <form action="" method="post">{% csrf_token %}
      {{ form.title }}
      {{ form.content }}
      {{ form.tags }}
      <button type="submit" class="btn btn-primary">发 布</button>
    </form>
  </div>
</div>
{% endblock %}

{% block scripts %}
<script type="text/javascript">
  var editor = new Editor();
  editor.render();
</script>
{% endblock %}
```

可以看到此模板中的 `{% block extra_head %}` 在父类模板 `blog_base.html` 中并不存在，所以还需要在 `blog/templates/blog_base.html` 文件中的 `<head>` 标签中添加 `{% block extra_head %}{% endblock %}`

* `{% block extra_head %}` 中的三个资源文件都是编辑器 [Editor](https://github.com/lepture/editor) 需要的，将资源文件单独放在这个页面引用，是因为只有这个页面才会用到这三个资源文件，其它页面不需要则不必进行引用了，免得拖慢了页面加载速度。
* `{% block scripts %}` 中是进行初始化一个 `Editor`，它会自动嵌套到页面的第一个 `<textarea>` 标签中。
* 以上的两点就是编辑器 [Editor](https://github.com/lepture/editor) 的用法了，很简单是吧。

####form 表单

```jinja
<form action="" method="post">{% csrf_token %}
  {{ form.title }}
  {{ form.content }}
  {{ form.tags }}
  <button type="submit" class="btn btn-primary">发 布</button>
</form>
```
这个 form 表单看起来很简单，讲解一下：

* `{% csrf_token %}` 是 [django 中的一个 CSRF 保护机制](https://docs.djangoproject.com/en/1.8/ref/csrf/)，在模板中的每个 `<form>` 标签后**必须**加上 `{% csrf_token %}`
* 上述代码中的 `title`、`content`、`tags` 均一一对应在 `blog/forms.py` 中的定义

###5. 查看效果

于浏览器地址栏中输入地址：http://192.168.1.221:8000**/blog/article/publish** （替换成你自己的 ip 和端口号）

效果图如下：

![](/img/blog_article_publish_001.png)

可看出文章标题和标签样式不是很好，我们将在接下来的章节中修改它。但可以看到主体部分 —— 编辑器已经显示无误了。


###6. 发布第一篇文章

按照下图填写好（也可以自己自由发挥）信息后，点击发布，发布成功后会跳转到 http://192.168.1.221:8000**/blog/** ，即博客首页（非网站首页，注意区分概念）

注：通常来说，在文章发布成功之后，应该跳转到此文章页面。但是由于咱现在并未定义访问文章的 `url` 链接，所以先暂时跳转到博客首页，在后续会讲解跳转到文章页面。

![](/img/blog_article_publish_002.png)

###7. 访问

因并未在 `blog/urls.py` 中定义访问文章的 `url` ，所以现在还不能通过浏览器访问刚发布的文章。

不过，咱可以去数据库中看看刚那篇文章存进去没有：

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
11 rows in set (0.00 sec)
```
```mysql
mysql> select * from blog_article;
+----+--------------------+-----------+----------+--------+---------------------------------------------------------------------+------------------------------------------------------------------------------------------------+-------------+-------+---------------------+---------------------+
| id | url                | title     | title_zh | author | content_md                                                          | content_html                                                                                   | tags        | views | created             | updated             |
+----+--------------------+-----------+----------+--------+---------------------------------------------------------------------+------------------------------------------------------------------------------------------------+-------------+-------+---------------------+---------------------+
|  1 | /article/test.html | test.html | 测试     |        | #测试

just for test.

[Django 教程](http://blog.10ns.top/)     | <h1>测试</h1>
<p>just for test.</p>
<p><a href="http://blog.10ns.top/">Django 教程</a></p>     | test 测试   |     0 | 2015-07-16 06:49:21 | 2015-07-16 06:49:21 |
+----+--------------------+-----------+----------+--------+---------------------------------------------------------------------+------------------------------------------------------------------------------------------------+-------------+-------+---------------------+---------------------+
1 row in set (0.00 sec)
```

虽然有点乱，不过还是看到有数据，已经存进去了。

看了看数据，发现：

1. `author` 是为空的（这个是因为没有登录，登录后即可解决。登录咱在后续章节再说）
2. `created` 和 `updated` 时间好像不对，看了看，有条警告：
```python
/home/shengan/env_django/local/lib/python2.7/site-packages/django/db/models/fields/__init__.py:1474: RuntimeWarning: DateTimeField Article.created received a naive datetime (2015-07-16 14:49:21.407130) while time zone support is active.
```
解决方案：

1. 忽略此警告
2. 将 `settings.py` 中的 USE_TZ 设置为 False：`USE_TZ = False`
{% endraw %}
