---
title: 某某电商商城逆向分析之二, 首页列表点击的页面跳转管理简单分析
hugo.Data: 2020-05-23
date: 2020-05-23
draft: false
tags:
categories:
  - iOS
---

> 不涉及逆向相关内容, 仅做分析功能
>
> 仅作为技术研究, 如有侵权, 请及时联系删除

电商类应用列表布局、页面跳转复杂, 很难写push/present vc, 一般是通过类似web的route那样, 定义一套route参数来统一处理页面跳转。

### 开始分析

> 这里主要分析某某电商商城首页列表的点击页面跳转

上篇文章提到parser把json解析为cell item, 其中cell item还包含用于页面跳转的action数据。

	action数据因各个app的业务不一而定义, 比如login(是否需要登录)、modal(是否使用模态视图)、path(跳转路径)、className(vc类名)、extraDict(额外的数据)...

列表上的item被点击之后, 调用delete的方法, 传递cell item(包含action数据), 最终到vc进行处理。

	action handler统一管理了页面跳转, 也是个log打点的好地方

action handler根据vc传递action数据处理各种页面跳转情况, 如push/present vc、scheme调用、打开网页(内部、外部)...
