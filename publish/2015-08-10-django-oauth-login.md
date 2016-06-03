{% raw %}
##第三方账号登录

此篇文章讲讲登录时常见到的第三方账号登录，比如国内的 `QQ`，`微博`等；国外的 `Gmail`，`Facebook` 等。

如今我们所见的第三方登录大部分都是使用的是 `Oauth` 方式，现今基本上使用的都是 `Oauth2.0`

此篇文章，我将以 `Github` 为例，讲述 `Oauth` 接入的流程。

###1. Oauth2.0 的基本流程

以下这六步摘自[阮一峰的相关博文](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)

1. 用户打开客户端以后，客户端要求用户给予授权。
2. 用户同意给予客户端授权。
3. 客户端使用上一步获得的授权，向认证服务器申请令牌。
4. 认证服务器对客户端进行认证以后，确认无误，同意发放令牌。
5. 客户端使用令牌，向资源服务器申请获取资源。
6. 资源服务器确认令牌无误，同意向客户端开放资源。

说说我对上述六步的理解：

1. 就是点击第三方登录的按钮，发送请求。比如登录页面上的第三方图标 ![](/img/github_login_logo_s.png)
2. 用户输入了第三方的登录名和密码
3. 在第 2 步的时候，第三方会返回 `code`，此步就是通过这个 `code` 去获取 `access token`
4. 第三方确认无误，返回 `access token`
5. 根据获取的 `access token` 去获取用户的基本信息
6. 第三方确认无误，返回用户信息


###2. 具体实例（以 Github 为例）

在 [Github 中有 Oauth2.0 接入的详细流程](https://developer.github.com/v3/oauth/)，以下是我实现的方式，供以参考：

####1. 先于 Github 中申请

![](/img/django_oauth_login_001.png)

申请后获得 `Client ID` 和 `Client Secret`

####2. 在页面添加接入第三方的入口

一般是在登录页面添加，有时候在注册页面也有。

此入口的 `url` 根据各个第三方来确定，比如 `github` 中的就为这个：

![](/img/django_oauth_login_002.png)

1. `client_id` 是必须的参数
2. `redirect_uri` 简单的说就是在验证后，第三方数据发送到你网站上的地址（我是单独新建了一个 `oauth` 的 `app`，专门用来 `oauth` 的登录）
3. `state` 参数本来是用来作为一个安全机制（防止 `csrf` 攻击的），我只是用来了作为验证后的重定向 `url` ~~ （你们不要学我）

####3. 代码实现

a. 新建 `oauth app`

```python
python manage.py startapp oauth
```

b. 添加 `url`

编辑 `oauth/urls.py` 文件：

```python
from django.conf.urls import url

from views import GithubOauthView

urlpatterns = [
    url(r'^github$', GithubOauthView.as_view(), name='github_oauth'),
]
```

c. 添加 `view`，实现 `GithubOauthView` 视图类

编辑 `oauth/views.py` 文件：

```python
import urllib
import urllib2
import json

from django.conf import settings
from django.contrib.auth.models import User
from django.views.generic.base import RedirectView
from django.contrib.auth import authenticate, login


GITHUB_CLIENTID = settings.GITHUB_CLIENTID
GITHUB_CLIENTSECRET = settings.GITHUB_CLIENTSECRET
GITHUB_CALLBACK = settings.GITHUB_CALLBACK

class GithubOauthView(RedirectView):
    permanent = False
    url = None

    def get_access_token(self):
        code = self.request.GET.get('code')
        url = 'https://github.com/login/oauth/access_token'
        data = {
            'grant_type': 'authorization_code',
            'client_id': GITHUB_CLIENTID,
            'client_secret': GITHUB_CLIENTSECRET,
            'code': code,
            'redirect_uri': GITHUB_CALLBACK,
        }
        data = urllib.urlencode(data)
        req = urllib2.Request(url, data, headers={'Accept': 'application/json'})
        response = urllib2.urlopen(req)
        result = response.read()
        result = json.loads(result)
        return result

    def get_user_info(self, access_token):
        url = 'https://api.github.com/user?access_token=%s' % (access_token)
        response = urllib2.urlopen(url)
        html = response.read()
        data = json.loads(html)
        username = data['login'] + '(github_oauth)'
        email = 'oauth@oauth.com'
        password = '********'
        try:
            user = User.objects.get(username=username)
        except:
            user = User.objects.create_user(username, email, password)
            user.save()
        user = authenticate(username=username, password=password)
        login(self.request, user)

    def get_redirect_url(self, *args, **kwargs):
        self.url = self.request.GET.get('state')
        try:
            access_token_info = self.get_access_token()
            access_token = access_token_info['access_token']
            self.get_user_info(access_token)
        except:
            pass
        return super(GithubOauthView, self).get_redirect_url(*args, **kwargs)
```

讲解一下：

1. 从上述可以看到，我仅仅只是使用了用户名，然后定义了其邮箱和密码，直接登录了。（很简单的使用）
2. 可对照着 [github 上的文档](https://developer.github.com/v3/oauth/) 来看这个代码。
3. 可以看到我将申请获得 `Client ID` 和 `Client Secret` 都存在了 `settings.py` 中。

```python
GITHUB_AUTHORIZE_URL = 'https://github.com/login/oauth/authorize'
GITHUB_CLIENTID = '4bd219ba99f0c174c5ae'  ##这个数据是真的，只是打乱了顺序~~~
GITHUB_CLIENTSECRET = '6f43f03d35f91944f6643055c33e7be7d68b92d9'  ##同上
GITHUB_CALLBACK = 'http://10ns.top/oauth/github'
```

一般在获取到用户的数据之后，可

1. 绑定在已有的用户上
2. 让用户完善信息，就是添加邮箱密码等信息后再登录
3. 像我这样懒的，直接登录


代码就不上传到 `github` 了，因为放在 `xblog` 上时，不能进行测试~~

###3. 最后说说此网站上的实现

现在网站上的代码就是使用的上述示例，我只是简单的用来获取用户的用户名，其它的均未使用。

`QQ` 和`微博`代码格式均与 `Github` 一样，仅仅有些细节不同，具体参照其各自的官网介绍。

{% endraw %}
