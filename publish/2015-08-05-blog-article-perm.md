{% raw %}
##为发布/编辑文章添加权限控制

之前在介绍发布和编辑文章时，你应该发现了，不管有没有登录，都可以进行发布和编辑文章，这肯定不是我们想要的。

这里所说的权限控制就是，简单的设置成只有管理员才能进行发布和编辑文章。

分两步：

###1. 在页面端对于非管理员用户隐藏发布/编辑文章按钮（链接）

以发布文章为例：
```jinja
{% if user.is_staff %}
	<a class="btn btn-primary" href="{% url 'article_publish' %}" role="button">发布新文章</a>
{% endif %}
```

###2. 在 views.py 中添加
```python
from django.contrib.admin.views.decorators import staff_member_required

class AdminRequiredMixin(object):
    @classmethod
    def as_view(cls, **initkwargs):
        view = super(AdminRequiredMixin, cls).as_view(**initkwargs)
        return staff_member_required(view)
```

然后让 `ArticlePublishView` 和 `ArticleEditView` 类继承 `AdminRequiredMixin` 即可。

###3. 然后你可以测测

直接在浏览器中输入发布文章的链接地址：http://192.168.1.221:8000**/blog/article/publish**

若不是管理员用户，则会直接跳转到管理员登录页面。
{% endraw %}
