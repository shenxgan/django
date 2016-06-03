{% raw %}
##环境配置

###写在前面
若你打算按照我这个教程来学习的话，并且对系统那些什么的都没有特殊其它要求的话，建议也希望你能够直接使用我的环境配置


###创建虚拟 Python 开发环境

####1. 安装 virtualenv
```shell
sudo pip install virtualenv
```

####2. 新建一个虚拟环境
```shell
virtualenv env_django
```
安装过程如下，就表示成功了
```shell
shengan@ubuntu:~$ virtualenv env_django
New python executable in env_django/bin/python
Installing setuptools, pip...done.
```
看看目录结构
```shell
shengan@ubuntu:~$ tree -L 2 env_django/
env_django/
├── bin
│   ├── activate
│   ├── activate.csh
│   ├── activate.fish
│   ├── activate_this.py
│   ├── easy_install
│   ├── easy_install-2.7
│   ├── pip
│   ├── pip2
│   ├── pip2.7
│   ├── python
│   ├── python2 -> python
│   └── python2.7 -> python
├── include
│   └── python2.7 -> /usr/include/python2.7
├── lib
│   └── python2.7
└── local
    ├── bin -> /home/shengan/env_django/bin
    ├── include -> /home/shengan/env_django/include
    └── lib -> /home/shengan/env_django/lib

9 directories, 12 files
```

####3. 进入虚拟环境
```shell
shengan@ubuntu:~$ source env_django/bin/activate
(env_django)shengan@ubuntu:~$ 
```
在命令行前面的 `(env_django)` 就表示你所处的就是 Python 虚拟开发环境中了，在其中安装的 Python 模块对系统不会产生任何影响。

####4. 退出虚拟环境
```shell
(env_django)shengan@ubuntu:~$ deactivate 
shengan@ubuntu:~$ 
```

###安装 Django
####1. 使用 pip 进行安装
```shell
(env_django)shengan@ubuntu:~$ pip install django
```
指定版本安装可使用
```shell
pip install Django==1.8.3	##我使用的就是这个版本
```
####2. 检测是否安装成功
在虚拟环境中进入 python 命令行环境
```shell
(env_django)shengan@ubuntu:~$ python
```
```python
>>> import django
>>> print django.get_version()
1.8.3
>>> 
```

至此，Django 开发环境已经初步安装配置完成。


###建议
若你是有计划的进行学习 django 的，建议你做到上述之后，即刻将这些初始的代码使用版本控制系统进行管理起来，推荐使用 git/svn，
若使用 git 则还有部分建议：

* 推荐你使用 [github](https://github.com/)
* 到 [https://github.com/github/gitignore](https://github.com/github/gitignore) 下载 Python.gitignore 文件（此文件也可在 github 中创建一个项目的时候选择，选择后不必再下载）。或使用 wget 直接进行下载

		wget https://raw.githubusercontent.com/github/gitignore/master/Python.gitignore

	将此文件移到 .git 目录并重命名为 .gitignore  （目的是为了在 git 版本库中忽略一些不必要的非代码文件，比如 pyc 文件等）
{% endraw %}
