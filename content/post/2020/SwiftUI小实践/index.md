---
title: SwiftUI小实践
hugo.Data: 2020-03-27
date: 2020-03-27
draft: false
tags:
categories:
  - iOS
---


### SwiftUI小实践 - 重写之前的Authenticator
> 对于之前做的一个TOTP两步验证器, 之前决定用SwiftUI重写一遍。后来工作忙, 一直拖到了过年时才完成。
> 
> 地址: <https://github.com/skytoup/Authenticator>, 只为了把功能做出来, 还没怎么整理代码
> 
> 已上架AppStore, 可直接下载体验: <itms-apps://itunes.apple.com/app/id1509275023>

### 功能
- 增、删、改、查、排序(double作为score)验证码
- 相册、相机扫描二维码导入
- 二维码导出
- watchOS同步数据, 生成动态码
- 数据同步到个人iCloud(仅AppStore版本有, 因为需要开发者计划才能开启此功能)


### 小经验

- 基本视图布局
	- 对于`List`的`Item`添加`onTapGesture`, 点击空白地方无法触发, 使用`.contentShape(Rectangle())`可解决
	- 单独隐藏`List`的分割线`ListSeparatorNoneViewModifier.swift`
- 定时刷新生成的验证码
	- `MyTimer.swift`
	- 使用`Timer.publish`发现很多奇奇怪怪的问题
	-  比如暂停和恢复, 实现出来的之后发现恢复会失败, 最后还是自定义一个`Timer`
- 配合CoreData刷新数据
	- `CodeList.swift`
	- 输入关键字查找功能, 使用`FetchRequest`, 放在`View`的属性里面, 在`View`初始化时根据关键字生成`FetchRequest`
- UIViewController混用
	- `LBXScan.swift`, `ImagePicker.swift`
	- 还是比较简单, 新建一个Struct, 实现`UIViewControllerRepresentable`即可
- watchOS
	- `Watch Extension/CodeView.swift`
	- 对于`watchOS`实在好, Storeboard很对UI都没法实现, 用了`SwiftUI`发现UI的可定制型更高了

实践的经验不算多, 希望对大家有帮助😄