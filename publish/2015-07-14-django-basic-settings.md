{% raw %}
##settings.py 文件的基本设置

刚开始仅需修改以下两项即可：

```python
LANGUAGE_CODE = 'zh-CN'
TIME_ZONE = 'Asia/Shanghai'
```

* 设置 `LANGUAGE_CODE` 为 `zh-CN` 可让 django 显示中文；
* 设置 `TIME_ZONE` 为 `Asia/Shanghai`，即为设置时区为本地时区。

在 settings.py 文件中此两项的默认值为：

```python
LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'
```

BTW.  
此处仅介绍 settings.py 最最基本也是最初的两项设置。如果你想要的是 settings.py 中其它的一些设置，请关注后续章节。
{% endraw %}
