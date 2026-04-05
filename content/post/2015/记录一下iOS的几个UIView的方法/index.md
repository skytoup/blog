---
title: 记录一下iOS的几个UIView的方法
hugo.Data: 2015-08-04
date: 2015-08-04
draft: false
tags:
categories:
  - iOS
---

在用自动布局的时候，老是忘记更新Constraint使用哪个方法，特意去查了一下资料，做了一下笔记。
如果出现错误的地方，希望大家指出，谢谢。

### UIView:
`// 重写此方法，当约束更新时，可更新你的特殊约束，别忘记调用super方法
- (void)updateConstraints;`

`// 调用这个方法，会触发update Constraints的操作，即更新约束。在needsUpdateConstraints返回YES时，才能成功触发update Constraints的操作。我们不应该重写这个方法。
- (void)updateConstraintsIfNeeded;`

`// 会调用drawRect方法
- (void)setNeedsDisplay;`

`// 会默认调用layoutSubViews
- (void)setNeedsLayout;`

`// 当一个自定义的View某一个属性的改变可能影响到界面布局，我们应该调用这个方法来告诉布局系统在未来某个时刻需要更新。系统会调用updateConstraints去更新布局。
- (void)setNeedsUpdateConstraints;`

`// 布局系统使用这个返回值来确定是否调用updateConstraints
- (void)needsUpdateConstraints;`

`// 如果有刷新的标记(应该是指调用过-(void) setNeedsLayout这个方法吧)，立即调用layoutSubviews进行布局
-(void) layoutIfNeeded;`

`// 自动调用layoutIfNeeded，当使用基于约束的布局基本实现适用于基于约束的布局，否则什么也不做。
- (void)layoutSubviews;`