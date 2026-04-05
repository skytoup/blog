---
title: SwiftUI初次体验
hugo.Data: 2019-10-17
date: 2019-10-17
draft: false
tags:
categories:
  - iOS
---


前几天macOS 10.15正式版推出, 于是把电脑升级了, 顺便体验下SwiftUI

经过一番基础学习, 计划把之前的项目(<https://github.com/skytoup/Authenticator>)尝试使用SwiftUI实现, 发现List的坑挺多的

- List相对于TableView提供的功能有点少, 还不太习惯, 而且问题也挺多的
	- 配合CoreData使用是很爽, 但是发现编辑模式时, 选中功能不正常了, 勾选row和selection的数据无法匹配, 得自行实现选中逻辑和UI才能解决
	- row左/右滑动菜单系统没有提供, TableView是提供了代理方法可简单实现该功能, 有人推荐使用ContextMenu(用起来也方便)替代
		- 延伸另一个问题, ContextMenu的按钮文字、图标按钮没法改颜色
	- 拖动row到上一行时, 把数据源交换之后, 动画很奇怪(拖动的row回到原位, 然后动画显示拖动的row到拖动目标的位置), 其它拖栋没发现问题(向下一/多行, 向上多行)

暂时也只是发现这么多

SwiftUI使用起来也是很舒服, 体验类似在写前端vue, 数据更新自动修改view, 省了很多更新代码, 还能实时预览和操作

~~ 东西还没完成, 继续研究补坑去... ~~
已用SwiftUI改写了所有界面: <https://github.com/skytoup/Authenticator>