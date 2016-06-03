{% raw %}
##静态文件

此篇文章将要介绍在开发环境中静态文件的管理与访问。

（与开发环境对应的就是生产环境，在生产环境中静态文件一般使用 web 服务器 `Apache` 或 `Nginx` 来管理，生产环境的部署在后续章节进行讲述。）

###settings.py

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = (
     os.path.join(BASE_DIR, "static"),
)
```

第一句在 `settings.py` 中默认是存在的，加上后三句是因为咱打算将 `static` 目录放在与 `manage.py` 同一目录下。

1. 在 `manage.py` 目录下新建 `static` 目录
2. 在 `static` 目录中新建 `img`、`css`、`js` 目录
3. 拷贝一个图像文件到 `img` 目录，比如 `logo.png`

###模板中使用

```jinja
{% load staticfiles %}

<img src="{% static 'img/logo.png' %}">
```

模板中使用必须要加上 `{% load staticfiles %}` 之后，方可使用 `{% static 'path' %}` 来引用静态文件。

可在博客首页进行测试。

###添加 blog.css

于 `static/css/` 目录中新建 `blog.css` 文件，作为整个博客系统自定义的布局文件。

编辑 `blog/templates/blog_base.html` 文件：

* 文件最开始处添加：

```jinja
{% load staticfiles %}
```

* 在 `<head>` 标签中添加：

```jinja
<link rel="stylesheet" href="{% static 'css/blog.css' %}">
```

完成上述两步，就成功的引用好了此 `blog.css` 文件，接下来就是在 `blog.css` 文件中添加你的 `css` 代码了。
{% endraw %}
