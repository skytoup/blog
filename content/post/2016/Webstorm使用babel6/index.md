---
title: Webstorm使用babel6
hugo.Data: 2016-01-28
date: 2016-01-28
draft: false
tags:
categories:
  - node.js
---

# 一、初始化工程
```javascript
npm init 'project name'
```
输入各种信息之后，新建工程完毕。

# 二、安装babel
```javascript
npm install --save-dev babel-cli
```

# 三、修改Webstorm配置
打开Setting -> Languages & Frameworks -> JavaScript -> JavaScript language version，选择ECMAScript6。

# 四、安装babel预插件
```javascript
npm install babel-preset-es2015 --save-dev
```

# 五、创建.babelrc配置文件
```
echo '{ "presets": ["es2015"] }' > .babelrc
```
或自行在项目根目录创建`.babelrc`文件，并输入`{ "presets": ["es2015"] }`，保存、退出。

# END
	tip: 运行的时候选择转换好的文件，别选原文件！！！
	
更详细请参考官方说明: [Babel_Webstorm](http://babeljs.io/docs/setup/#webstorm)