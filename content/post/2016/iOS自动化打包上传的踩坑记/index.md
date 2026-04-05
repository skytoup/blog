---
title: iOS自动化打包上传的踩坑记
hugo.Data: 2016-05-31
date: 2016-05-31
draft: false
tags:
categories:
  - iOS
---

	2016-09-11:
	1.添加使用Python封装打包命令的开源库(最下面~)
	2016-06-06:
	1.经过一轮测试之后, 发现文章有点错漏, 小修改一下, 增加命令四
> 每次要出包的时候, 总要打开`XCode`, 然后点击`Product-Archive`, 等待好几分钟的各种build, 然后还要手动上传到`AppStore`, 甚至还要上传到`蒲公英`、`fir`等...

很久以前就看了很多关于`iOS`自动打包`ipa`的文章, 看着感觉很简单, 但是因为一直没有`AppleDeveloper`账号可以给我用, 到了真的要搞自动打包的时候, 才发现到处都是坑。

# 基本命令
1. xcedebuild: 生成`Archive`、导出`ipa`, 还有其它功能...
2. xcrun: 把`*.app`打包成`ipa`, 还有其它功能...

# 基本使用
一. `xcedebuild`打包`Archive`文件

	xcedebuild -workspace ${path to *.xcworkspace} -scheme ${scheme} -destination generic/platform=iOS archive -configuration Release ONLY_ACTIVE_ARCH=NO -archivePath ${export path *.xcarichive}

* -workspace 你的`*.xcworkspace`文件	
* -scheme 项目文件里面的`scheme`	
* -archivePath 生成的`*.xcarichive`文件路径


二. `xcedebuild`从`*.xcarchive`导出`ipa`

	xcodebuild -exportArchive -exportFormat IPA -archivePath ${path to *.xcarchive} -exportPath ${export path *.ipa} -exportProvisioningProfile ${ProvisioningProfileName}
	
* -archivePath 你的`*.xcarchive`文件, 可以使用上一个命令导出
* -exportPath 导出的`ipa`路径
* exportProvisioningProfile 你的Distribution发布证书的名称(只需要名称)

三. `xcrun`打包`ipa`

	xcrun -sdk iphoneos PackageApplication -v ${path to *.app} -o ${package path *.ipa}
	
* -v 你的`*.app`文件, 生成的`*.xcarchive`里面有
* -o 打包生成的`*.ipa`文件路径, 注意！！！这里是不能填相对路径, 因为这里的路径环境变量不是当前执行命令的路径了

四. 最新的正确`xcodebuild`导出ipa
	
	xcodebuild -exportArchive -archivePath ${path to *.xcarchive} -exportPath ${export path to dir} -exportOptionsPlist ${path to export options *.plist}
	
* -archivePath 你的`*.xcarchive`文件, 可以使用第二个命令导出
* -exportPath 导出的`ipa`目录, ipa的名称好像是scheme的名称
* -exportOptionsPlist 导出plist格式的配置文件

exportOptionPlist: 新建一个plist文件, 里面是一个Dictionary, key-value如下, 都是可选值, 不需要全部填上
> 1. compileBitcode: Bool
> > For non-App Store exports, should Xcode re-compile the app from bitcode? Defaults to YES.
> 
> 2. embedOnDemandResourcesAssetPacksInBundle : Bool
> > For non-App Store exports, if the app uses On Demand Resources and this is YES, asset packs are embedded in the app bundle so that the app can be tested without a server to host asset packs. Defaults to YES unless onDemandResourcesAssetPacksBaseURL is specified.
>
> 3. iCloudContainerEnvironment
> > For non-App Store exports, if the app is using CloudKit, this configures the "com.apple.developer.icloud-container-environment" entitlement. Available options: Development and Production. Defaults to Development.
>
> 4. manifest : Dictionary
> > For non-App Store exports, users can download your app over the web by opening your distribution manifest file in a web browser. To generate a distribution manifest, the value of this key should be a dictionary with three sub-keys: appURL, displayImageURL, fullSizeImageURL. The additional sub-key assetPackManifestURL is required when using on demand resources.
>
> 5. method : String
> > Describes how Xcode should export the archive. Available options: app-store, ad-hoc, package, enterprise, development, and developer-id. The list of options varies based on the type of archive. Defaults to development.
>
> 6. onDemandResourcesAssetPacksBaseURL : String
> > For non-App Store exports, if the app uses On Demand Resources and embedOnDemandResourcesAssetPacksInBundle isn't YES, this should be a base URL specifying where asset packs are going to be hosted. This configures the app to download asset packs from the specified URL.
>
> 7. teamID : String
> > The Developer Portal team to use for this export. Defaults to the team used to build the archive.
>
> 8. thinning : String
> > For non-App Store exports, should Xcode thin the package for one or more device variants? Available options: <none> (Xcode produces a non-thinned universal app), <thin-for-all-variants> (Xcode produces a universal app and all available thinned variants), or a model identifier for a specific device (e.g. "iPhone7,1"). Defaults to <none>.
>
> 9. uploadBitcode : Bool
> >	 For App Store exports, should the package include bitcode? Defaults to YES.
>
> 10. uploadSymbols : Bool
> > For App Store exports, should the package include symbols? Defaults to YES.

# 踩坑
## 坑一
使用第一个命令前, 需要确保你的项目的签名配置好, 证书下好最新的

## 坑二
第二个命令的`-exportProvisioningProfile`填的只是你的发布证书的名称, 不是那一串`id`

## 坑三
第二个命令打包出来的`*.ipa`不能用来上传到`AppStore`, 一直报CocoaPods里面的第三方库签名错误

## 坑四
打包出来的`*.ipa`需要上传到`AppSotre`的话, 可以使用第三个命令, `xcrun`那一个

## 坑五
使用第三个命令打包出来的`*.ipa`, 上传到`AppStore`之后, 登录到`iTunes Connect-APP-所有构建版本`查看到上传的`*.ipa`正在构建。但是过了一会儿, `AppleDeveloper`账号的邮箱就会收到一封报错的邮件, 大概是说你的`*.ipa`包里面, 缺少了一个`SwiftSupport`文件夹

经过各种搜索之后, 原来需要把`xcrun`打包出来的`*.ipa`解压, 然后新建一个文件夹, `*.xcarchive`里面的`SwiftSupport`文件夹`copy`进去, 还有把`ipa`解压出来的`move`进入, 最后打个zip包, 再改成`ipa`后缀就可以上传到`AppStore`了(应该吧, 还没测试😂)

## 坑六
经过一轮测试之后, 发现用这个`xcrun`这个命令打的包需要自己吧SwiftSupport文件加到压缩包, 其实有一个命令没有那么麻烦的...

使用上面的第四个命令使用`*.xcarchive`把ipa导出, 导出的`*.ipa`里面会包含了`SwiftSupport`, 还不需要自己把它加进去

# 番外篇
在踩到了第五个坑之后, 在`github`发现了一个`iOS`的打包、发布库...	
上地址: <https://github.com/nomad/shenzhen>		
懒得自己搞的可以使用这个库, 感觉还是挺不错的

# Python封装打包命令
github: [package-ipa](https://github.com/skytoup/package-ipa)