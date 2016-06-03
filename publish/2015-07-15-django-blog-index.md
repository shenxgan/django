{% raw %}
##博客首页

问：

当你脑袋想着我要开始写一个博客程序时，针对博客本身，你首先想到的是什么？

我想到的是要先有博客首页~

好，那就来看看博客首页有哪些东西，又该怎么来实现。

###博客首页元素

大家应该对此不是很陌生，相信各位都见过他人的博客了；

基本上博客（博客首页）上有以下几种元素（从上至下，从左至右）：

1. 菜单导航栏
2. 博客主视区（主体），常见的会分成两栏或三栏
3. 博客底部，常见的是站点地图链接、备案号等放在这里

嗯，基本上一个简单的博客首页就是这些元素了。

###公共元素
虽在上面我将博客元素分成了三类，但很明显 导航栏和底部栏 可认为是不变的，也就是所谓的公共栏（每个页面都会有）

对于公共元素，我们在设计代码时，就可以将其提取出来，然后其他模板去包含它即可。


###代码部分

####1. [导航条](http://v3.bootcss.com/components/#navbar)
在 `blog/templates/` 目录下新建 `blog_nav.html` 文件：

```shell
shengan@ubuntu:~/mysite/blog/templates$ vim blog_nav.html
```
```html
<nav class="navbar navbar-default navbar-static-top" role="navigation">
  <div class="container">
    <div class="navbar-header">
      <button class="navbar-toggle collapsed" type="button" data-toggle="collapse" data-target=".bs-navbar-collapse">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a href="{% url 'blog_index' %}" class="navbar-brand">xblog</a>
    </div>
    <div id="navbar" class="collapse navbar-collapse bs-navbar-collapse">
      <ul class="nav navbar-nav">
        <li><a href="#">Link</a></li>
      </ul>
      <ul class="nav navbar-nav navbar-right">
        <li><a href="#">Link</a></li>
      </ul>
    </div>
  </div>
</nav>
```
导航条是按照 `bootstrap` 来写的，详细的可去其[中文官网](http://v3.bootcss.com/components/)查看。

####2. 模板基类 `blog_base.html`

接着新建 `blog_base.html` 文件：

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.4/css/bootstrap.min.css">
    <script src="http://cdn.bootcss.com/jquery/1.11.2/jquery.min.js"></script>
    <script src="http://cdn.bootcss.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
  </head>
  <body>
    {% block nav %}
      {% include "blog_nav.html" %}
    {% endblock %}

    {% block content %}{% endblock %}

    {% block footer %}{% endblock %}

    {% block scripts %}{% endblock %}
  </body>
</html>
```
这三句是导入 bootstrap 核心文件，使用的是 CDN 加速服务。

[http://v3.bootcss.com/getting-started/#download](http://v3.bootcss.com/getting-started/#download)

```html
<link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.4/css/bootstrap.min.css">
<script src="http://cdn.bootcss.com/jquery/1.11.2/jquery.min.js"></script>
<script src="http://cdn.bootcss.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
```

`<body>` 标签中的 `{% block %}{% endblock %}` 是 [django 模板](https://docs.djangoproject.com/en/1.8/ref/templates/builtins/#std:templatetag-block)中的语法。简要的来说就是定了 block 模块后，在子模板中可以重写它。

####3. 更新博客首页

更新 `blog_index.html` 文件：

```jinja
{% extends 'blog_base.html' %}

{% block content %}
<p>{{ test }}</p>
<p>{{ welcome }}</p>
{% endblock %}
```

`{% extends 'blog_base.html' %}` 中 extends 为 [django 模板](https://docs.djangoproject.com/en/1.8/ref/templates/builtins/#extends)中的语法。简要的说就是扩展/继承父模板。


####4. 浏览器访问博客首页，查看效果

![](/img/django_blog_index_001.png)

当屏幕宽度小到一定程度时，效果图如下：

![](/img/django_blog_index_002.png)
{% endraw %}
