---
title: Python3的虚拟环境
hugo.Data: 2016-08-28
date: 2016-08-28
draft: false
tags:
categories:
  - Python
---

# 背景
Python安装第三方模块的时候, 是安装到系统全局的环境。当你的多个项目里面用到了同一个库, 但是版本却不一样, 这样就会产生冲突了。

# pyvenv(还有一个比较好的非官方工具Virtualenv就不介绍了)
pyvenv是Python3安装时自带的创建一个虚拟环境工具(tip: Python3.4版本前的pyven创建的虚拟环境不带pip)

用这个工具就能创建出多个Python的虚拟环境, 把第三方模块安装到不同的虚拟环境, 就能让不同的项目使用不同的Python环境, 互相不会受到影响。

# 基本使用(Mac、Linux, Win很久没用了)
创建虚拟环境命令:
	
	pyvenv /path/to/new/virtual/environment

激活虚拟环境命令:

	source /path/to/virtual/environment/bin/activate

退出虚拟环境命令:

	deactivate

例子:
```
pyvenv py_1-evn # 当前目录创建一个虚拟环境叫py_1-evn
source py_1-evn/bin/activate # 在本终端激活这个虚拟环境(没有激活虚拟环境时是使用全局的环境)
pip install 。。。。 # 可以安装各种第三方模块, 都会安装到激活的虚拟环境
python xxx.py # 在激活的虚拟环境运行py脚本
deactivate # 退出激活的虚拟环境
```

# 更高级的使用(Python API)
请参考官方文档: <https://docs.python.org/3/library/venv.html>
