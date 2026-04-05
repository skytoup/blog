---
title: centos7安装puppeteer依赖
hugo.Data: 2018-08-31
date: 2018-08-31
draft: false
tags:
categories:
  - 
---

`python`使用`request-html`时, 发现`pyppeteer`报了几个错误, 然后越来越多`chrome`的进程

查了下原因, 原来`pyppeteer`只是下载了`chromium`, 但是没有安装需要的依赖

安装补上即可

	yum install pango.x86_64 libXcomposite.x86_64 libXcursor.x86_64 libXdamage.x86_64 libXext.x86_64 libXi.x86_64 libXtst.x86_64 cups-libs.x86_64 libXScrnSaver.x86_64 libXrandr.x86_64 GConf2.x86_64 alsa-lib.x86_64 atk.x86_64 gtk3.x86_64 ipa-gothic-fonts xorg-x11-fonts-100dpi xorg-x11-fonts-75dpi xorg-x11-utils xorg-x11-fonts-cyrillic xorg-x11-fonts-Type1 xorg-x11-fonts-misc -y