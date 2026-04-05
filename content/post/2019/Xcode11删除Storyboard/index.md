---
title: Xcode11删除Storyboard
hugo.Data: 2019-10-06
date: 2019-10-06
draft: false
tags:
categories:
  - iOS
---


iOS 13支持多窗口, 引入了Screen相关API。App的AppDelegate部分已经改变, 想把Storeboard删掉, 增加UIWindow已经不是在AppDelegate里面修改。

整理出以下步骤(Xcode11 Swift5.1)

1. 删除Main.storyboard文件
2. Info.plist删除 Application Scene Manifest - Scene Configuration - Application Session Role - 0 - Storyboard Name
3. 在ScreenDelegate添加window

```
	guard let scene = (scene as? UIWindowScene) else { return }
        
    self.window = UIWindow(windowScene: scene)
    self.window?.backgroundColor = .white
    
    let rootVC = UIViewController()
    let winRootVC = UINavigationController(rootViewController: rootVC)
    self.window?.rootViewController = winRootVC

    self.window?.makeKeyAndVisible()
```
