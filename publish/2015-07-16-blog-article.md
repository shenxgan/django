{% raw %}
##文章展示

在之前我们已经做好了首页框架，内容可先不用理会。（到时候自己弄出一个自己喜欢的博客首页就行）

今天就来看看如何发布一篇博文并展示出来。

一篇文章从无到有，需要【书写（编辑）--发布--存储--展示--评论】等这些个步骤，在接下来的几个小章节，都会依据此顺序进行讲述。

###Markdown 书写博客

[Markdown 语法](http://wowubuntu.com/markdown/)很简单，基本上你对照着语法过一遍就会了，估计十分钟。

在书写完博客之后，因用的是 Markdown 编写的，所以还需要将 Markdown 格式的文本进行转换成 HTML 格式，以便在页面进行展示。

在 python 中，安装 markdown 模块即可。

进入虚拟环境，pip 安装 markdown：

```shell
(env_django)shengan@ubuntu:~/mysite$ pip install markdown
```

###文章编辑器
因我们采用 Markdown 来书写文章，所以我们需要一个支持 Markdown 编辑预览的编辑器（编辑器不是必须的，其主要作用也只是用于预览功能）。

在前面章节介绍过，我们采用的是开源编辑器 [Editor](https://github.com/lepture/editor)。这个教程就是在它上写出来的。总体感觉是很方便、小巧，无论是看起来还是用起来都很舒服。

####使用方法：
其使用方法我们将在下节《发布文章》中介绍。
{% endraw %}
