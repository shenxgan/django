{% raw %}
##代码高亮

一般如果你网站的后台程序是 `python` 的时候，我都会推荐你使用 [Pygments](http://pygments.org/)  进行代码高亮。

###1. 安装 Pygments

```shell
pip install Pygments
```

###2. 下载 css 文件

去 [github](https://github.com/richleland/pygments-css) 下载 css 文件（我使用的是 `emacs.css`）放入 `static/css` 目录；

然后在 `<head>` 标签中引用它，比如：

```jinja
<link rel="stylesheet" href="{% static 'css/emacs.css' %}">
```

###3. markdown 解析

添加 `'markdown.extensions.codehilite'`

```python
html = markdown.markdown(md, extensions=['markdown.extensions.extra', 'markdown.extensions.codehilite'])
```

###4. 贴一段 python 代码，看看效果

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

'''
费波那契数列
费波那契数列由0和1开始，之后的费波那契系数就由之前的两数相加。
'''

def fibonacci(n):
    x,y = 0,1
    while n > 0:
        yield x
        x,y,n = y,x+y,n-1


for fib in fibonacci(13):
    print fib,
```

###5. 另一个简单的方案

使用 [highlight.js](https://highlightjs.org/)，这个用法也很简单，这里就不介绍了（个人感觉是高亮效果不如 `Pygments`）。
{% endraw %}
