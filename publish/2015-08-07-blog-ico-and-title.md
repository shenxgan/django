{% raw %}
##添加网站图标和网页标题

网站图标和每个网页上的标题对于网站的重要性不言而喻，在这里为不清楚的人作一个介绍：

###1. 添加网站图标

在 `<head>` 标签中添加：

```jinja
<link rel="icon" href="{% static 'img/logo.png' %}" type="image/x-icon">
<link rel="shortcut icon" href="{% static 'img/logo.png' %}" type="image/x-icon">
```

这样，刷新页面后就可以看到刚刚设置的图标了。


###2. 为每个页面添加标题

每个页面的标题都需要咱们自己去定，但是对于文章来说，可使用其中文标题来设置成其页面标题。

标题的形式是这样的，也在 `<head>` 标签中：

```html
<title>titlename</title>
```

具体实现：
####1. 在 `<head>` 标签中添加：

```jinja
{% block title %}{% endblock %}
```

####2. 在文章模板 `blog/templates/article_detail.html` 中添加：

```jinja
{% block title %}
<title>{{ object.title_zh }} | xblog</title>
{% endblock %}
```

其它不能以变量作为标题的页面只能自己手动自定了。
{% endraw %}
