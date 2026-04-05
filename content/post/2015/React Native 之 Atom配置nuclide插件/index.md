---
title: React Native 之 Atom配置nuclide插件
hugo.Data: 2015-12-09
date: 2015-12-09
draft: false
tags:
categories:
  - React
---

1. 首先下载[Atom](https://atom.io)，下载地址:<https://atom.io>
2. 打开`Atom`，在右上角`Atom`的菜单处选择`Install Shell Comments`(安装apm)
	![shell](http://7xoq5y.com1.z0.glb.clouddn.com/屏幕快照%202015-12-09%2023.37.56.png)
3. 到github下载一下`nuclide`，直接用git或者下载zip文件解压。
    
    	项目地址: https://github.com/facebook/nuclide
		git命令: git clone https://github.com/facebook/nuclide
4. 安装`nuclide`前，需要电脑有以下环境

		- python 2.6 or later
		- Atom 0.209.0 or later
		- Node 0.12.0 or latr
		- node、npm、apm、git在你的$PATH
		(node、npm可通过安装node.js来安装)
执行一下命令
	
		cd nuclide
		./script/dev/setup
		# 上面的命令会通过npm下载东西，可能比较久
		apm link
		# 注意，上面的命令是把当前目录 软链接 到 ~/.atom/packages 目录下，所以安装完了之后，别把nuclide这个文件夹删了！！！

	搞定之后，打开Atom试一下是否安装成功，如果不成功，`Atom`会提示你rebuild插件，我也不知道什么问题，我安装的时候失败了，点了几次rebuild就突然成功了。

5. 配置一下`nuclide-flow`吧，这个插件可以检测语法错误，`CMD+左键点击`跳转。

	使用这个插件之前，请先安装`flow`，安装完之后，到`Atom`的`Setting-Packages`，找到`nuclide`的插件，进入设置里面，滚动到`nuclide-flow`插件的设置，把flow的位置填上，根据个人喜好打上几个选项的钩。
	![flow_1](https://img-blog.csdn.net/屏幕快照%202015-12-09%2023.38.13)
	![flow_1](https://img-blog.csdn.net/屏幕快照%202015-12-09%2023.38.33)
		
6. 另外有几个插件也不错

		- atom-beautify 代码格式化
		- atomerminal 打开终端，pwd为当前文件所在路径
		- docblockr 写注释的插件

	可以通过`apm install xxx`来安装，如`apm install atom-beautify`。
	
接下来就可以好好享受Atom了~~~