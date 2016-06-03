{% raw %}
##用户登录

进入 django 管理后台时，也有一个登录页面，但是那是管理员用户用来登录到管理后台的，而不是普通用户的登录页面。

咱们要讲述的是[ django 自带的用户登录](https://docs.djangoproject.com/en/1.8/topics/auth/default/)的使用：

###使用 django 自带的用户身份验证视图

很简单，只需要在 `mysite/mysite/urls.py` 中添加：

```python
url('^', include('django.contrib.auth.urls'))
```
或者，你也可以改成：

url(r'^accounts/', include('django.contrib.auth.urls'))


它包含了如下的这些 url：

```python
^login/$ [name='login']
^logout/$ [name='logout']
^password_change/$ [name='password_change']
^password_change/done/$ [name='password_change_done']
^password_reset/$ [name='password_reset']
^password_reset/done/$ [name='password_reset_done']
^reset/(?P<uidb64>[0-9A-Za-z_\-]+)/(?P<token>[0-9A-Za-z]{1,13}-[0-9A-Za-z]{1,20})/$ [name='password_reset_confirm']
^reset/done/$ [name='password_reset_complete']
```

咱，先来实现 login，`login` 默认的模板是 `registration/login.html`，此模板需要自己手动去创建，其官网有一个例子，直接拿过来使用即可。

首先在 `blog/templates/` 目录下创建 `registration/` 目录，然后在 `blog/templates/registration/` 目录创建 `login.html` 文件：

```jinja
{% extends "blog_base.html" %}

{% block content %}

{% if form.errors %}
<p>Your username and password didn't match. Please try again.</p>
{% endif %}

{% if next %}
    {% if user.is_authenticated %}
    <p>Your account doesn't have access to this page. To proceed,
    please login with an account that has access.</p>
    {% else %}
    <p>Please login to see this page.</p>
    {% endif %}
{% endif %}

<form method="post" action="{% url 'django.contrib.auth.views.login' %}">
{% csrf_token %}
<table>
<tr>
    <td>{{ form.username.label_tag }}</td>
    <td>{{ form.username }}</td>
</tr>
<tr>
    <td>{{ form.password.label_tag }}</td>
    <td>{{ form.password }}</td>
</tr>
</table>

<input type="submit" value="login" />
<input type="hidden" name="next" value="{{ next }}" />
</form>

{# Assumes you setup the password_reset view in your URLconf #}
<p><a href="{% url 'password_reset' %}">Lost password?</a></p>

{% endblock %}
```

讲解：

1. `{% if user.is_authenticated %}`，`is_authenticated` 是 `user` 对象自带的方法，用来检测用户是否登录。
2. `{% url 'password_reset' %}`，密码重置链接。点击进入密码重置页面。

###访问查看效果

http://192.168.1.221:8000**/login/**

如果你在 `url` 中加入了 `accounts`，那么就访问：http://192.168.1.221:8000**/accounts/login/**

效果图：

![](/img/django_user_login_001.png)

布局可先不管，或者按照自己的想法自己布局，或者可看我这个博客的登录页面，或者到时候参考我给的示例 css 文件。


###设置登录成功后的跳转页面

在登录成功后，默认的跳转页面是 `/accounts/profile/`；可在 `settings.py` 文件中修改，比如添加：

```python
LOGIN_REDIRECT_URL = '/blog/'
```

这样，在登录成功后，会跳转到博客首页上。
{% endraw %}
