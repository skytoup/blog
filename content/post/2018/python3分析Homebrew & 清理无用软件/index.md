---
title: python3分析Homebrew & 清理无用软件
hugo.Data: 2018-06-27
date: 2018-06-27
draft: false
tags:
categories:
  - python
---

Homebrew是MacOSX上的软件包管理工具, 可以方便安装各种Apple没有预装的东西。安装软件时, 还会自动安装依赖, 但是卸载时, 依赖不会被卸载。(本人有洁癖, 喜欢把没用的东西都清理掉)

## 获取安装列表
Homebrew有提供命令

	brew deps --installed
	
然后会出现一堆软件及其依赖, 例如:
	
	autoconf: 
	automake: autoconf
	carthage: 
	cmake: 
	python@2: gdbm openssl readline sqlite
	readline: 
	sqlite: readline
	tree: 
	usbmuxd: libplist libusb
	watchman: gdbm openssl pcre python@2 readline sqlite
	...
	
数量少的话, 人工分析也是可以的, 然而平时乱装东西(安装是在方便)...

## 分析(python3)
1. 执行命令, 获取依赖列表

	```	
	# 代码均为片段, 完整代码请到下面的源码地址
	with os.popen('brew deps --installed') as popen:
		dep_lines = popen.read().split('\n')
	```

2. 	解析列表

	```
	# 代码均为片段, 完整代码请到下面的源码地址
	class BrewPackage:
		def __init__(self, name: str):
			self.name = name
			self.deps = []
			self.is_dep = False

		def __repr__(self) -> str:
			return '{}: {}'.format(self.name, self.deps)


	for line in dep_lines:
		if not line:
			continue
	        
		result = line.split(': ')
		name = result[0]
		deps_name = result[1].split(' ') if len(result) == 2 and result[1] else None
		
		package = BrewPackage(name)
		
		if not deps_name:
			continue
		for dep_name in deps_name:
			dep_package = BrewPackage(name)
			dep_package.id_dep = True
			package.deps.append(dep_package)
	```

3. 数据整理

	```
	# 代码均为片段, 完整代码请到下面的源码地址
	# 上面第二步, 把BrewPackage缓存起来(每一个name只存在一个BrewPackage), 然后数据就会变成一张单向图结构.
	
	all_package_dict = {}  # name -> package
	
	
	def get_cache_package(package_name: str) -> BrewPackage:
	    pkg = all_package_dict.get(package_name)
	    if not pkg:
	        pkg = BrewPackage(package_name)
	        all_package_dict[package_name] = pkg
	    return pkg
	    
	# 把第二部创建BrewPackage的部分, 换成get_cache_package即可
	```

4. 输出结果

	```
	# 代码均为片段, 完整代码请到下面的源码地址
	# 为了更好看的输出结果, BrewPackage重写__repr__方法
	
	@classmethod
    def _deps_str(cls, package, level: int = 0, need_deep: bool = True):
        ts = '\t' * (level + 1)
        s = ''
        for p in package.deps:
            s += '{}- {}\n'.format(ts, p.name)
            if need_deep and p.deps:
                s += cls._deps_str(p, level + 1)
        return s

	def __repr__(self):
		return '{}:\n{}'.format(self.name, BrewPackage._deps_str(self))
		
	-------------------------------------------------------------------------
	
	# 输出所有软件包
	print('All package and deps')
	print('--------------------')
	for package in all_package_dict:
		print(package)
	print('\n')

	# 这里print出来的包, 都没有被依赖, 看看那个不认识或不想要都可以卸载掉, 卸载完一遍后, 需要再次运行该程序分析
	# 如此循环, 直到没有找到需要卸载的包, 这样就清理干净了
	print('Not dependent packages')
	print('----------------------')
	not_dep_package = [p for p in all_package_dict.values() if not p.is_dep]
	for p in not_dep_package:
		print(p.name)
	```

完整地址: 

- [github](https://github.com/skytoup/brew_deps)
- [gitee](https://gitee.com/skytoup/brew_deps)