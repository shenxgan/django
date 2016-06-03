{% raw %}
##Templates

在之前咱们输出 `Hello, world.` 的时候，仅仅是作为 `HttpResponse` 的参数，并未使用模板。咱今天就来为它加上模板。

```python
return HttpResponse("Hello, world.")
```
首先看看了解一下模板的语法，再为它加上模板。

###模板的语法

####1. 变量（Variables）
模板中使用变量时，需要将其用两个大括号括起来（`{{ VariableName }}`），如下：

```jinja
My first name is {{ first_name }}. My last name is {{ last_name }}
```

####2. 标签（Tags）
标签用于模板中的逻辑实现，需要包含在 `{% %}` 之间，比如：`{% csrf_token %}`

有些标签还需要结束标签：
```jinja
{% if user.is_authenticated %}
  Hello, {{ user.username }}.
{% endif %}
```

常用的标签有 `if`， `for` 等，查看详细请[点击这里](https://docs.djangoproject.com/en/1.8/ref/templates/builtins/#ref-templates-builtins-tags)

####3. 过滤器（Filters）
过滤器是主要用来对变量进行额外的一些操作，比如获取长度（`length`）、格式化日期（`date`）等

过滤器格式类似如下：
```jinja
{{ string|length }}
{{ datetime|date:"Y-m-d h:i:s" }}	{# 对应格式是 年-月-日 时:分:秒 #}
```

###修改 blog/views.py
更新 blog/views.py 文件：
```python
from django.shortcuts import render

def blog_index(request):
    context = {
        'test': 'just for test.',
        'welcome': 'hello world.'
    }
    return render(request, 'blog_index.html', context)
```

`blog_index.html` 为模板文件，需要自己动手新建：

* 在当前目录下（`blog/` 目录）新建 `templates` 目录。
* 在 `blog/templates/` 目录下新建 `blog_index.html` 文件，文件内容如下：

```html
<p>{{ test }}</p>
<p>{{ welcome }}</p>
```

* 再次访问 `http://10.0.0.46:8000/blog/`，可看到输出：
```text
just for test.

hello world.
```
{% endraw %}
