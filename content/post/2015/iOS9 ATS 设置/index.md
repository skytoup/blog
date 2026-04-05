---
title: iOS9 ATS 设置
hugo.Data: 2015-11-28
date: 2015-11-28
draft: false
tags:
categories:
  - iOS
---

iOS9有一个新特性--ATS(App Transport Security)，加强了一下App网络传输安全。

[官方文档描述](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html#//apple_ref/doc/uid/TP40016198-SW14):
---------
> App Transport Security (ATS) enforces best practices in the secure connections between an app and its back end. ATS prevents accidental disclosure, provides secure default behavior, and is easy to adopt; it is also on by default in iOS 9 and OS X v10.11. You should adopt ATS as soon as possible, regardless of whether you’re creating a new app or updating an existing one.
> 
> If you’re developing a new app, you should use HTTPS exclusively. If you have an existing app, you should use HTTPS as much as you can right now, and create a plan for migrating the rest of your app as soon as possible. In addition, your communication through higher-level APIs needs to be encrypted using TLS version 1.2 with forward secrecy. If you try to make a connection that doesn't follow this requirement, an error is thrown. If your app needs to make a request to an insecure domain, you have to specify this domain in your app's Info.plist file.

	大概意思就是iOS9和OS X 10.11不能直接使用HTTP，只能使用HTTPS，现在开始，最好计划好迁移工作。

实际上，没有强制一定要使用HTTPS，如果需要使用HTTP，可以到Info.plist文件配置一下。

### ATS基本使用(大概用Json格式表示)
1. 在Info.plist的Information Property List下添加NSAppTransportSecurity，类型是Dictionary。
2. 在NSAppTransportSecurity下添加NSAllowsArbitraryLoads，类型是Boolean，值是YES。

```
{
	NSAppTransportSecurity: {
		NSAllowsArbitraryLoads : YES
	}
}
```

		大概意思就是，配置ATS，设置为允许使用任意的HTTP/HTTPS。
		
### ATS进阶使用(大概用Json格式表示)
1、允许单个服务器的HTTP连接:

```json
{
	AppTransportSecurity: {
		NSExceptionDomains: {
			允许HTTP访问的地址: {
				NSExceptionAllowsInsecureHTTPLoads : YES
			}
		}
	}
}
```

2、允许单个服务器的HTTPS使用较低的安全协议

```json
{
	AppTransportSecurity: {
		NSExceptionDomains: {
			允许HTTP访问的地址: {
				NSExceptionRequiresForwardSecrecy : NO,
				NSExceptionMinimumTLSVersion : TLSv1.0
			}
		}
	}
}
```

3、使用ATS，且允许使用HTTP访问指定的服务器

```json
{
	AppTransportSecurity: {
		NSExceptionDomains: {
			允许HTTP访问的地址: {
				NSExceptionAllowsInsecureHTTPLoads : YES
			}
		},
		NSAllowsArbitraryLoads : YES
	}
}
```

4、指定服务器使用ATS，其余的不使用
```json
{
	NSAppTransportSecurity: {
		NSAllowsArbitraryLoads = YES,
		NSExceptionDomains: {
			使用HTTPS访问的地址: {
				NSExceptionAllowsInsecureHTTPLoads : NO
			}
		}
	}
}
```