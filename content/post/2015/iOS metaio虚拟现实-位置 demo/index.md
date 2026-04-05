---
title: iOS metaio虚拟现实-位置 demo
hugo.Data: 2015-08-27
date: 2015-08-27
draft: false
tags:
categories:
  - iOS
---

# LocationARTest
### 测试环境：Xcode 6，iOS 7.0(真机)以上。
基于Metaio的demo修改出来的，查看周边的美食

github链接 : https://github.com/skytoup/LocationARTest

![GIF](1.gif)

## 注意
本demo不能直接运行

* 需要修改JHKey.h中的JH_ID(修改为你的聚合id，并到聚合数据网站申请数据)

		聚合数据账号注册：http://www.juhe.cn/
		数据申请：http://www.juhe.cn/docs/api/id/45
		
* 需要修改Info.plist中的MetaioLicenseString

		MetaioLicense申请：http://metaio.com/

* 由于MetaioSDK.framework有300+M，github限制单文件100M，所以无法上传

		请自行到Metaio官网下载http://metaio.com/

### 用到的其他工具
[JsonToModule](https://github.com/skytoup/JsonToModule):一个命令行工具，能把json文件转换成java或者objc的模型类

### 用到的第三方库
* [metaioSDK.framework](http://metaio.com/)
* [JuheApisSDK](http://www.juhe.cn/)