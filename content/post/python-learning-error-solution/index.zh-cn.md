+++
author = "Darkblue Soul"
title = "Python Crash Course学习笔记"
date = "2022-04-18 08:52:10 +0800"
description = "Python Crash Course学习时碰到的一些问题及解决办法"
categories = [
    "Python"
]
tags = [
    "error", "API", "active", "Django", "models"
]
image = "python-course.jpg"

+++
最近在学《Python Crash Course》第一版，可能是版本的原因，抄作业经常会出错，网上找了下解决办法。
## Error--requests.exceptions.ProxyError【17.1.4　处理API响应】
调用API一直报错，请求异常，代理错误。
参考:[令人不悦的Error--requests.exceptions.ProxyError](https://blog.csdn.net/qq_39241986/article/details/80457017)
python_repos.py修改：
```python
# 执行API调用并存储响应
url = 'https://api.github.com/search/repositories?q=language:python&sort=stars'
r = requests.get(url)
print("Status code:", r.status_code)
```
为：
```python
# 执行API调用并存储响应
url = 'https://api.github.com/search/repositories?q=language:python&sort=stars'
# 绕过系统代理
proxies = { "http": None, "https": None}
r = requests.get(url, proxies=proxies)
print("Status code:", r.status_code)
```

## 无法加载文件Scripts\Activate.ps1【18.1.4　激活虚拟环境】
`ll_env\Scripts\activate`一直激活不了虚拟环境，报错无法加载文件。
参考：[Win10 VS Code pipenv无法加载文件Scripts\Activate.ps1](https://blog.csdn.net/weixin_43031092/article/details/113412866)
我是直接在VS自带的终端输入`Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`回车，然后就好了。

## Error: Requested setting INSTALLED_APPS, but settings are not configured【18.1.6　在Django中创建项目】
新建项目`django-admin.py startproject learning_log .`报错。
参考：[Python [解决方法] error: Requested setting INSTALLED_APPS, but settings are not configured](https://blog.csdn.net/zjiang1994/article/details/52680731)
修改：
```powershell
django-admin.py startproject learning_log .
```
为：
```powershell
django-admin startproject learning_log .
```

## TypeError: __init__() missing 1 required positional argument: ‘on_delete‘【18.2.5　迁移模型Entry】
`python manage.py makemigrations learning_logs`出错，显示`TypeError: __init__() missing 1 required positional argument: ‘on_delete‘`。

参考：[【异常】TypeError: __init__() missing 1 required positional argument: ‘on_delete‘](https://blog.csdn.net/yu12345_/article/details/120039586)，在【18.2.4　定义模型Entry】models.py定义外键时加上位参on_delete。
修改：
```python
class Entry(models.Model):
    """学到的有关某个主题的具体知识"""
    topic = models.ForeignKey(Topic)
```
为：
```python
class Entry(models.Model):
    """学到的有关某个主题的具体知识"""
    topic = models.ForeignKey(Topic, on_delete=models.CASCADE)
```
