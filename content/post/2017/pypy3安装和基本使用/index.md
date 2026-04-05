---
title: pypy3安装和基本使用
hugo.Data: 2017-07-28
date: 2017-07-28
draft: false
tags:
categories:
  - Python
---

## 安装

### MacOS
1. 先安装`brewhome`, 官网: [brewhome](https://brew.sh/)(已安装的跳过)
2. 打开终端, 输入命令`brew install pypy3`(没翻墙的可能比较慢)
3. 安装pypy的pip, 在终端输入`curl -O https://bootstrap.pypa.io/get-pip.py && pypy3 get-pip.py`

### CentOS
待测试...

## 基本使用

### 基本命令
- `pypy3`: 运行脚本, 相当于`python3`
	- `pypy3 xxx.py` # 运行脚本xxx.py
	- `pypy3 -m pip install xxx` # 安装依赖xxx
	- `pypy3 -m venv xxx` # 创建一个虚拟环境到xxx目录
		- 切换到该虚拟环境: `source xxx/bin/activate`, 退出虚拟环境: `deactivate`
- `pip_pypy3`: 安装依赖库, 相当于 `pip3`
	- `pip_pypy3 install xxx` # 安装依赖xxx
	- `pip_pypy3 install -r requirement.txt` # 递归安装requirement.txt里面的依赖
- `easy_install_pypy3`: 安装依赖库, 相当于`easy_install3`
	- `easy_install_pypy3 xxx` # 安装依赖xxx

### 测试性能
```Python3
# test.py
from time import time

times = 10
start_time = time()

for t in range(times):
    sum = 0
    st = time()
    for i in range(10 ** 6):
        sum += i
    print('{}: speed time: {}'.format(t, time()-st))

total_time = time()-start_time
print('speed time avrage: {}, total: {}'.format(total_time / times, total_time))
```

> python3 test.py

![img](http://7xoq5y.com1.z0.glb.clouddn.com/python.gif)

	0: speed time: 0.16138482093811035
	1: speed time: 0.1585860252380371
	2: speed time: 0.1659379005432129
	3: speed time: 0.1676487922668457
	4: speed time: 0.16137290000915527
	5: speed time: 0.16071820259094238
	6: speed time: 0.16538310050964355
	7: speed time: 0.1611499786376953
	8: speed time: 0.17231488227844238
	9: speed time: 0.17661476135253906
	speed time avrage: 0.165160608291626, total: 1.6516060829162598


> pypy3 test.py

![img](http://7xoq5y.com1.z0.glb.clouddn.com/pypy.gif)

	0: speed time: 0.005280017852783203
	1: speed time: 0.003924131393432617
	2: speed time: 0.002089977264404297
	3: speed time: 0.0024471282958984375
	4: speed time: 0.0020821094512939453
	5: speed time: 0.0025098323822021484
	6: speed time: 0.002827882766723633
	7: speed time: 0.0020329952239990234
	8: speed time: 0.002518892288208008
	9: speed time: 0.001984119415283203
	speed time avrage: 0.002937006950378418, total: 0.02937006950378418
	
简单的测试来看, pypy比cpython快了好几倍