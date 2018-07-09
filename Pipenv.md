---
title: Pipenv：Python项目环境与依赖管理工具
date: 2018-07-09 18:16:31
tags: 
- Python
- Pipenv
---

### **Pipenv**
> Pipenv就是Requests的作者Kenneth Reitz写出来的，我们可以把它看做pip和virtualenv的结合



记录下Pipenv，方便以后查看



#### **安装Pipenv**

> pip install pipenv



#### **创建虚拟环境**

> pipenv install



```python
treehl@ssaw:~/env_test$ pipenv install
Creating a Pipfile for this project...
Pipfile.lock not found, creating...
Locking [dev-packages] dependencies...
Locking [packages] dependencies...
Updated Pipfile.lock (dfae9f)!
Installing dependencies from Pipfile.lock (dfae9f)...
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 0/0 — 00:00:00
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.

```



#### **激活虚拟环境**

> pipenv shell

```python
treehl@ssaw:~/env_test$ pipenv shell
Launching subshell in virtual environment…
treehl@ssaw:~/env_test$  . /home/treehl/.local/share/virtualenvs/env_test-jaLbPeP-/bin/activate
(env_test-jaLbPeP-) treehl@ssaw:~/env_test$ 

```



#### **安装包**

这里安装两个包，依赖会添加到Pipfile文件中

> pipenv install requests django

```python
(env_test-jaLbPeP-) treehl@ssaw:~/env_test$ pipenv install requests django
Installing requests...
Collecting requests
  Downloading https://files.pythonhosted.org/packages/65/47/7e02164a2a3db50ed6d8a6ab1d6d60b69c4c3fdf57a284257925dfc12bda/requests-2.19.1-py2.py3-none-any.whl (91kB)
Collecting idna<2.8,>=2.5 (from requests)
  Downloading https://files.pythonhosted.org/packages/4b/2a/0276479a4b3caeb8a8c1af2f8e4355746a97fab05a372e4a2c6a6b876165/idna-2.7-py2.py3-none-any.whl (58kB)
Collecting certifi>=2017.4.17 (from requests)
  Downloading https://files.pythonhosted.org/packages/7c/e6/92ad559b7192d846975fc916b65f667c7b8c3a32bea7372340bfe9a15fa5/certifi-2018.4.16-py2.py3-none-any.whl (150kB)
Collecting chardet<3.1.0,>=3.0.2 (from requests)
  Downloading https://files.pythonhosted.org/packages/bc/a9/01ffebfb562e4274b6487b4bb1ddec7ca55ec7510b22e4c51f14098443b8/chardet-3.0.4-py2.py3-none-any.whl (133kB)
Collecting urllib3<1.24,>=1.21.1 (from requests)
  Downloading https://files.pythonhosted.org/packages/bd/c9/6fdd990019071a4a32a5e7cb78a1d92c53851ef4f56f62a3486e6a7d8ffb/urllib3-1.23-py2.py3-none-any.whl (133kB)
Installing collected packages: idna, certifi, chardet, urllib3, requests
Successfully installed certifi-2018.4.16 chardet-3.0.4 idna-2.7 requests-2.19.1 urllib3-1.23

Adding requests to Pipfile's [packages]...
Installing django...
Collecting django
  Downloading https://files.pythonhosted.org/packages/bf/e0/e659df5b5b82299fffd8b3df2910c99351b9308b8f45f5702cc4cdf946e9/Django-1.11.14-py2.py3-none-any.whl (7.0MB)
Collecting pytz (from django)
  Downloading https://files.pythonhosted.org/packages/30/4e/27c34b62430286c6d59177a0842ed90dc789ce5d1ed740887653b898779a/pytz-2018.5-py2.py3-none-any.whl (510kB)
Installing collected packages: pytz, django
Successfully installed django-1.11.14 pytz-2018.5

Adding django to Pipfile's [packages]...
Pipfile.lock (dfae9f) out of date, updating to (b7e917)...
Locking [dev-packages] dependencies...
Locking [packages] dependencies...

```





#### **记录依赖**

**用virtualenv记录依赖，需要创建requirements.txt，每次添加或者删除新的依赖需要多次执行pip freeze > requierements.txt**



**使用Pipenv会自动帮我们管理依赖，在创建虚拟环境的时候会自动创建Pipfile和Pipfile.lock两个文件，在使用pipenv install和pipenv uninstall命令安装和卸载包的时候自动更新**

**目录下有Pipfile和Pipfile.lock**

- Pipfile用来记录项目依赖列表
- Pipfile.lock记录了固定版本的详细依赖包列表



#### **安装依赖**

> pipenv install





#### **查看依赖关系**

```python
(env_test-jaLbPeP-) treehl@ssaw:~/env_test$ pipenv graph
Django==1.11.14
  - pytz [required: Any, installed: 2018.5]
requests==2.19.1
  - certifi [required: >=2017.4.17, installed: 2018.4.16]
  - chardet [required: >=3.0.2,<3.1.0, installed: 3.0.4]
  - idna [required: >=2.5,<2.8, installed: 2.7]
  - urllib3 [required: >=1.21.1,<1.24, installed: 1.23]
```



#### **检查安装的包的安全性**

```python
(env_test-jaLbPeP-) treehl@ssaw:~/env_test$ pipenv check
Checking PEP 508 requirements...
Passed!
Checking installed package safety...
All good!

```



欢迎访问[Treehl的博客](https://family-treesy.github.io/)

[GitHub](https://github.com/Family-TreeSY)
