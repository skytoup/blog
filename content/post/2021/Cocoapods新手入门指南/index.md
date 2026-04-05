---
title: 2021 Cocoapods新手入门指南
hugo.Data: 2021-04-20 13:06-00:00
draft: false
tags:
categories:
  - iOS
date: 2021-04-20
---


> 老手可完全略过, 其它废话不多说...

### 一. 安装

打开终端(`Terminal`), 输入命令

`sudo gem install cocoapods`

还是提示无法安装可尝试

`sudo gem install cocoapods --user`

### 二. `pod`命令使用

- `pod init`
  - 在项目的根目录运行, 生成一份`Podfile`文件
  - 可自行创建`Podfile`文件, 不使用此命令
- `pod deintegrate`
  - 在项目根目录运行, 移除项目`cocoapods`依赖管理相关的所有设置(`*.xcodeproj`里面的`build setting`相关设置), 一般用不到
- `pod install`
  - 按照`Podfile`的设置(版本号、仓库索引源...)安装依赖库, 并且`生成`/`更新` `Podfile.lock`(记录依赖库的依赖关系、版本号、仓库索引源...)
- `pod update`
  - 按照`Podfile`的设置并结合`Podfile.lock`(必须有)进行版本升级

**一般使用**

0. `pod init # 初始化出一份Podfile文件`
1. `Podfile`文件内添加三方库
2. `pod install # 项目首次安装依赖库`
3. `pod update # 日常根据Podfile设定的规则进行版本更新`

**小技巧**
- `pod init`和`pod update`每次运行都会先更新`仓库索引源`数据, 略略费时间, 可以改为手动更新
  - 可在后面增加参数`--no-repo-update`, 可以`安装`/`更新`时不更新`仓库索引源`
    - `pod install --no-repo-update`
    - `pod update --no-repo-update`
  - 手动更新`仓库索引源`
    - `pod repo update`

### 三. Podfile

`pod init`生成的模板

```
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'iOSDemo' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for iOSDemo

end
```

- `platform`: 设定项目兼容的平台和版本号
- `target`: 某个`target`依赖库安装配置
  - 里面填写一些需要安装的依赖库和配置
    - `pod {依赖库的名称}`, 比如`pod 'AFNetworking'`, `pod 'AFNetworking', ~> 1.0`

```
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'iOSDemo' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for iOSDemo
  pod 'AFNetworking'
end
```

依赖库版本设置

- `= 0.1` 0.1版本
- `> 0.1` 任何大于0.1版本
- `>= 0.1` 任何大于或等于0.1版本
- `< 0.1` 任何小于0.1版本
- `<= 0.1` 任何小于或等于0.1版本
- `~> 0.1.2` 0.1.2(包括)到2.0.0(不包括)版本
- `~> 0.1.3-beta.0` beta的0.1.3版本, 和release的0.1.3(包括)到2.0.0(不包括)版本

> 版本语义说明(有兴趣可以了解下)
>> Semantic Versioning: <https://semver.org/lang/zh-CN/>
>> RubyGems Versioning Policies: <https://guides.rubygems.org/patterns/#semantic-versioning>

### 参考链接

- 官网: <https://cocoapods.org>
