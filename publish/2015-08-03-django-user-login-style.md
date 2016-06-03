{% raw %}
##用户登录--添加样式

此篇继上篇，主要讲述为登录页面添加样式。

###1. 了解一下 bootstrap 中 form 表单

在 [bootstrap 的 form 表单](http://v3.bootcss.com/css/#forms)样式中，其 `<input>` 标签的 `class` 均为 `form-control`，而在模板中我们使用
```jinja
{{ form.username }}
```
输出的是：
```html
<input type="text" name="username" maxlength="254" id="id_username">
```
可以看到其内并不包含 `class`，那该怎么添加 `class` 呢？

我来说说我理解的两种方法：

1. 继承 [AuthenticationForm](https://docs.djangoproject.com/en/1.8/_modules/django/contrib/auth/forms/#AuthenticationForm) 类（此类是默认的登录页面使用的表单类），重新定义 `username` 和 `password`。在定义 `form filed` 的时候通过 `widget` `attr` 来添加其 `class`

2. 想其它方法在默认表单的 `filed` 中添加 `class`

下面只讲述方法 2

###2. 为 form filed 添加 css class

通过网上搜索和自己想的，初步可确定有三种方法：

1. 自定义 `filter`，参考：[http://stackoverflow.com/questions/5827590/css-styling-in-django-forms](http://stackoverflow.com/questions/5827590/css-styling-in-django-forms)

2. 安装 `django-widget-tweaks` 来解决，参考：[http://treyhunner.com/2014/09/adding-css-classes-to-django-form-fields/](http://treyhunner.com/2014/09/adding-css-classes-to-django-form-fields/)

3. 在页面加载时使用 `js` 来添加 `class` （未验证，想来应该可以）

下面只讲述方法 1

###3. [自定义 filter ](https://docs.djangoproject.com/en/1.8/howto/custom-template-tags/)

在 `mysite/blog/` 目录下新建 `templatetags/` 目录，并在 `mysite/blog/templatetags/` 目录下新建 `myfilters.py` 文件（和 `__init__.py` 文件，不要忘了此文件），文件内容如下：

```python
#!/usr/bin/env python
# coding=utf-8

from django import template

register = template.Library()

@register.filter(name='add_class')
def add_class(value, arg):
    return value.as_widget(attrs={'class': arg})
```

###4. 在模板中使用自定义的 filter

在模板 `registration/login.html` 开始处添加：

```jinja
{% load myfilters %}
```
然后使用：

```jinja
{{ form.username|add_class:"form-control" }}
```

###5. 查看效果

登录页面的样式已全部修改，代码已经上传至 [github](https://github.com/shenxgan/xblog)。

效果图如下（和此博客登录页面一致）：

![](/img/django_user_login_style_001.png)
{% endraw %}
