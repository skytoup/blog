---
title: 把json转成java、objc的module的小工具
hugo.Data: 2015-08-06
date: 2015-08-06
draft: false
tags:
categories:
  - c
---

### 简介
	一个把json文件，转换为java、objcetive-c的module文件。
	暂不支持一个array的下一级，还是array
	简单方便地处理后台返回的json数据，不必要一个一个字段复制到module类上。

### github
	[json2module](https://github.com/skytoup/JsonToModule)

### 用到的第三方开源库
	cJSON c语言的json解析库

### 基本的实现逻辑
![实现逻辑](20150806202839772.png)

### 编译
	命令行进入到项目根目录后
	make

	测试:
		make test
		测试生成的文件在项目目录下的out_file里面

### 使用说明(English不是很好):
	json2moudle <json file path> [-n] [-o] [-p] [-h] [--java] [--objc]
	
	option:
	-n <module name> 	 	default is json file name
	-o <out path> 		 	default is run path
	-p <java pack name> 	default is ""
	-h 			 			help
	--java 			 		out java module file
	--objc 			 		out objective-c module file
	
	if not have --java or --objective-c, default is java
		
### 使用事例：
	json2moudle t.json
	json2moudle t.json -n test2 -o ~/Desktop/test -p test.com.hehe
	json2moudle t.json -n test2 -o ~/Desktop/test
	json2moudle t.json -n test2 -o ~/Desktop/test -p test.com.hehe --objc --java