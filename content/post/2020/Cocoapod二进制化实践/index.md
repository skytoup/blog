---
title: Cocoapod二进制化实践
hugo.Data: 2020-01-14
date: 2020-01-14
draft: false
tags:
categories:
  - iOS
---


> 2020-02-06
> 添加Swift 5.1开始, ABI稳定特性打包兼容说明

(`Carthage`暂不考虑..., 略过)

### 简(fei)介(hua)
随着越来越多方便的第三方组件, 现在开发App基本十多个第三方依赖以上. 没有`build cache`或者打正式包的时候, 每次基本耗时10分钟以上.

主要原因是需要编译大量源文件(大概分为app和Cocoapods依赖库), 所以把可抽离代码编译好, 再去引用就能减少源文件编译时间

至于有没有其它更高级玩法, 暂时还不太清楚

### 想法(来自网络各种方法综合)
简单的平民解决方法: 建立私有二进制的`repo`

- app
	- 抽离封装为一个库, 打包为二进制, 存到私有`Cocoapods repo`
- Cocoapods依赖
	- 每个组件版本的源码打包为二进制, 存到私有`Cocoapods repo`


如果公司或者你有足够资源, 可以弄`双私有源`(两个私有`repo`, 一个二进制, 一个源码), 不然就简单点, 弄个私有二进制的`repo`也够用


### 思路
由于没机器和资源, 只实践了后面的`简(diao)单(si)版`. 有条件最好是做一套自动化的, 我是没机器和资源, 打包还是各种脚本在自己机器上手动打包, 好惨...

简单版思路:

- 打包第三方源码
	- 每个tag打包二进制(只弄了打包`framework`, 不知道别人的`static framework`如何制作, 试了不成功)
- 私有repo
	- 其实就是一个`git`仓库, 私有属性即可(虽然公有也行, 只是别人能看), 一般`github`, `gitlab`, `gitee`或者`自建git`创建一个仓库就可以了
- 二进制文件存放
	- 打包出来的二进制文件, 需要找个地方提供下载, 有条件的话可以存CDN管理, 没的话在内网放个静态文件服务器存也行, 还有可以存`git`(需要有`http(s)`下载地址)...


### 实践
需要的一些辅助工具

- <https://github.com/tripleCC/cocoapods-bin>
- <https://github.com/tripleCC/binary-server>

步骤

- 打包第三方源码
	1. `git clone`各种组件源码库
	2. 脚本打包
		- `tag`拉分支, 并切换(打包之后删除, 再回去`master`)
		- 使用`pod gen`根据`podsec`创建工程
		- `xcodebuild`打包`framework`(真机和模拟器)
		- 合并真机和模拟器的`framework`
		- 看上去是这么简单, 实践的时候会发现有些库是不能通用一个脚本的
	3. 上传`framework`(用了`binary-server`管理)
- 二进制的`podsec`上传到私有的`repo`
	1. 使用`pod bin spec create`创建二进制版本的`podsec`
		- 有`subsec`的会要求提供`template`
		- 某些平台要求版本会低于8, 因为是使用`framework`, 需要平台版本需要改为8以上
	2. 上传生成的`podsec`
- 使用
	- 在`Podfile`加上`source {你的私有repo}`, 然后再加一个官方的repo(例如: `source 'https://cdn.cocoapods.org/'`). 
	- 官方的一定要加在后面, 有相同的库会根据`source`的顺序获取依赖. 想切回源码注释私有库那一行就好了(因为只是简单的一个`私有二进制repo`)

### 某些坑
- 不知道会有啥影响, `AppStore`的打包还是老老实实的用源码build
- `modulemap`记得要把真机和模拟器的合一起
- 真机和模拟器的`DSYM`文件保留(万一哪一天用上)
- 有些第三方库是`*.podsec`, 有些是`*.podsec.json`
- `pod bin spec create`创建的二进制`podsec`文件名是带加了`binary`, 需要去除, 不然和`podsec`的`name`和`文件名`对不上, 无法通过`pod spec lint`
- `pod repo push`上传私有`spec`的时候, 记得加`--use-json --allow-warnings`
- 或许还有其它...

### 其它
- Swift 5.1开始, ABI已稳定, 可打包兼容多个不同swift版本打包可相互使用, 不需要重新打包`framework`
	- 开启地方位于项目的`build setting` - `Build Option` - `Build Libraries for Distribution`改为YES
	- `framework`的`Modules`里面会多生成一个类似源码的interface文件
	- 相关资料
		- <https://developer.apple.com/videos/play/wwdc2019/402/>
