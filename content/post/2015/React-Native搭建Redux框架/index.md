---
title: React-Native搭建Redux框架
hugo.Data: 2015-12-24
date: 2015-12-24
draft: false
tags:
categories:
  - 
---

> 近段时间在学习React-Native，发现了还可以用flux思想进行开发，于是便想试一试。
> 
> 查了各种关于flux的资料之后，有好几个这类型的框架。但是Redux这个框架貌似比较多人使用，所以就选择这个框架了。
> 
> 选好框架之后，发现框架的介绍资料很少，而且没有搭建的教程，只好自己做一下实验了。经过无数次实验、踩坑，终于成功了........
> 
> 注: 我是一个iOS程序员，不太懂大前端，为了学习react-native，react只是随便学习了一点基础，若本文有错，望你能指出	

下面分享一下成果:

### 1. 搭建基本React-Native项目
很简单，大家都会，一句命令
```bash
react-native init 你的项目名称
```
	
### 2. 添加框架模块
新建完成基本项目后，打开package.json，在dependencies下，添加
```text
"react-redux": "^3.1.2"
```	

	有人或许想问，最新的`react-redux`最新版不是4.x么？为什么不用最新的。	
	这是一个坑，到github的项目主页看下面的文档，里面说4.x不支持`react-native`，请使用3.x的。
	
添加完成后，继续输入一下命令：
```bash
npm i # 安装刚才添加的项目
npm install --save redux
npm install --save redux-thunk
```

### 3. 修改项目结构
	以下是我的目录结构
	├── App
	│   ├── Action
	│   ├── Component
	│   ├── Container
	│   ├── Reducer
	│   ├── Store
	│   └── Main.js
	
### 4.创建Redux项目框架(相关名词不解析了，请自行学习Redux框架了解)
	这里就不详细说了，简单说一下搭建时，编辑文件的顺序估计就可以了，详细的编写内容，到我的github上clone一下示例项目就好了。

新建Action -->> 新建Redicer -->> 新建Store -->> 新建Main.js入口 -->> 修改android和iOS的入口文件(即项目根目录的index.xxx.js)

基本就是这样子了~

	最后附上详细代码的github地址: https://github.com/skytoup/react-native_ReduxTest

本文参考:	
1. react-native的Redux示例: <https://github.com/chentsulin/react-native-counter-ios-android>