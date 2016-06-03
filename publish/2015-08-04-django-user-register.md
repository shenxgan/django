{% raw %}
##用户注册

较之用户登录的不同，注册功能，`django` 中并不自带相应的 `url` 和 `view` 等。所以咱们需要从头开始写。

###1. urls.py

编辑 `mysite/urls.py` 文件，添加：

```python
url(r'^register/$', RegisterView.as_view(), name='register'),
```

###2. views.py

编辑 `mysite/views.py` 文件（初始在 `mysite` 目录是没有 `views.py` 文件的，首次需要自己手动创建），添加 `RegisterView` 类实现：

```python
from django.core.urlresolvers import reverse_lazy
from django.views.generic.edit import FormView
from django.contrib.auth import authenticate, login

from forms import RegisterForm

class RegisterView(FormView):
    template_name = 'register.html'
    form_class = RegisterForm
    success_url = reverse_lazy('blog_index')

    def form_valid(self, form):
        form.save()
        username = form.cleaned_data.get('username')
        password = form.cleaned_data.get('password')
        user = authenticate(username=username, password=password)
        login(self.request, user)
        return super(RegisterView, self).form_valid(form)
```

讲解一下：

1. 在 `form.save()` 执行完成后，新用户已经存入数据库了，后面的 `login` 即为用户登录，参考 [django 中的示例](https://docs.djangoproject.com/en/1.8/topics/auth/default/)

###3. forms.py

编辑 `mysite/forms.py` 文件（首次也需要自己手动创建），添加 `RegisterForm` 实现：

```python
#!/usr/bin/env python
# coding=utf-8
from django.contrib.auth.models import User
from django import forms

class RegisterForm(forms.Form):
    username = forms.CharField(
        label=u'昵称',
        help_text=u'昵称可用于登录，不能包含空格和@字符。',
        max_length=20,
        initial='',
        widget=forms.TextInput(attrs={'class': 'form-control'}),
        )

    email = forms.EmailField(
        label=u'邮箱',
        help_text=u'邮箱可用于登录，最重要的是需要邮箱来找回密码，所以请输入您的可用邮箱。',
        max_length=50,
        initial='',
        widget=forms.TextInput(attrs={'class': 'form-control'}),
        )

    password = forms.CharField(
        label=u'密码',
        help_text=u'密码只有长度要求，长度为 6 ~ 18 。',
        min_length=6,
        max_length=18,
        widget=forms.PasswordInput(attrs={'class': 'form-control'}),
        )

    confirm_password = forms.CharField(
        label=u'确认密码',
        min_length=6,
        max_length=18,
        widget=forms.PasswordInput(attrs={'class': 'form-control'}),
        )

    def clean_username(self):
        username = self.cleaned_data['username']
        if ' ' in username or '@' in username:
            raise forms.ValidationError(u'昵称中不能包含空格和@字符')
        res = User.objects.filter(username=username)
        if len(res) != 0:
            raise forms.ValidationError(u'此昵称已经注册，请重新输入')
        return username

    def clean_email(self):
        email = self.cleaned_data['email']
        res = User.objects.filter(email=email)
        if len(res) != 0:
            raise forms.ValidationError(u'此邮箱已经注册，请重新输入')
        return email

    def clean(self):
        cleaned_data = super(RegisterForm, self).clean()
        password = cleaned_data.get('password')
        confirm_password = cleaned_data.get('confirm_password')
        if password and confirm_password:
            if password != confirm_password:
                raise forms.ValidationError(u'两次密码输入不一致，请重新输入')

    def save(self):
        username = self.cleaned_data['username']
        email = self.cleaned_data['email']
        password = self.cleaned_data['password']
        user = User.objects.create_user(username, email, password)
        user.save()
```

说一下 clean，clean 是用来进行验证输入的。

1. `clean_field`，此函数是用来验证单个 `field`，验证无误后必须返回此 `field`
2. `clean`，此函数用来同时验证多个 `field`，不需要返回

###4. templates
最后一步来添加模板，因注册和登录是独立与博客之外（我认为注册登录应该属于整个站点）。所以我们将模板放在项目根目录下，即 `manage.py` 文件所在目录。

在 `manage.py` 文件所在目录添加 `templates/` 目录，然后在 `templates/` 下新建 `register.html` 文件：

```jinja
{% extends 'blog_base.html' %}

{% block content %}
<div class="container">
  {% if form.non_field_errors %}
  <div class="messages">
    <div class="alert alert-warning alert-dismissible" role="alert">
      <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
      {{ form.non_field_errors }}
    </div>
  </div>
  {% endif %}
  <div class="register center-block">
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3 class="panel-title"><strong>注册</strong></h3>
      </div>
      <div class="panel-body">
        <form action="" method="post">{% csrf_token %}
          <div class="form-group {% if form.username.errors %} has-error {% endif %}">
            <label for="">{{ form.username.label }}</label>
            {% if form.username.errors %}
            <span class="text-danger pull-right">{{ form.username.errors }}</span>
            {% endif %}
            {{ form.username }}
            <p class="text-muted">{{ form.username.help_text }}</p>
          </div>
          <div class="form-group {% if form.email.errors %} has-error {% endif %}">
            <label for="">{{ form.email.label }}</label>
            {% if form.email.errors %}
            <span class="text-danger pull-right">{{ form.email.errors }}</span>
            {% endif %}
            {{ form.email }}
            <p class="text-muted">{{ form.email.help_text }}</p>
          </div>
          <div class="form-group {% if form.password.errors %} has-error {% endif %}">
            <label for="">{{ form.password.label }}</label>
            {% if form.password.errors %}
            <span class="text-danger pull-right">{{ form.password.errors }}</span>
            {% endif %}
            {{ form.password }}
            <p class="text-muted">{{ form.password.help_text }}</p>
          </div>
          <div class="form-group {% if form.confirm_password.errors %} has-error {% endif %}">
            <label for="">{{ form.confirm_password.label }}</label>
            {% if form.confirm_password.errors %}
            <span class="text-danger pull-right">{{ form.confirm_password.errors }}</span>
            {% endif %}
            {{ form.confirm_password }}
          </div>
          <button type="submit" class="btn btn-default">注 册</button>
        </form>
      </div>
    </div>
  </div>
</div>
{% endblock %}
```
**注：**要使此 `templates/` 目录能够被 `django` 搜索识别，还需要在 `settings.py` 中进行设置：

```python
TEMPLATES = [
	{
		#...
		'DIRS': [os.path.join(BASE_DIR,'templates')],
		#...
	}
]
```
（同时我们将登录模板，位于 `blog/templates/` 目录下的 `registration/` 文件夹移动到 `templates/` 目录）

###5. 查看效果
http://192.168.1.221:8000**/register/**

 ![](/img/django_user_register_001.png)
{% endraw %}
