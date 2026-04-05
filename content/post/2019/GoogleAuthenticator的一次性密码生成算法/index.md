---
title: GoogleAuthenticator的一次性密码生成算法
hugo.Data: 2019-10-08
date: 2019-10-08
draft: false
tags:
categories:
  - 
---


### GoogleAuthenticator
使用于二次验证的`动态密码`生成工具, 一般用于登录验证、替代一些短信验证等...

其中`动态密码`生成算法为`HOTP`(HMAC-Based One-Time Passcode)和`TOTP`(Time-based One-Time Passcode)

`TOTP`和`HOTP`均基于`OTP`(One-Time Passcode)

### OTP
一次性密码生成基本公式: `OTP(K, C) = Truncate(EncryptionAlgorithm(K, C))`

- K
	- 秘钥
- C
	- 一个8 byte数据
- Trucate
	- 截取Hash数据组成验证码的函数
- EncryptionAlgorithm
	- 加密算法(对称加密算法, HASH, HMAC)

### HOTP和TOTP
一次性密码生成公式变更为: 

```
# HOTP
HOTP(K, C) = Truncate(HMAC-based(K, C))

# TOTP
TOTP(K, T) = Truncate(HMAC-based(K, T))
T = (CurrentUnixTime − T0) / X
```

HOTP参数变更:

- C
	- 计数器

TOTP参数变更:

- T
	- 时间点
- CurrentUnixTime
	- 当前Unix时间戳
- T0
	- 初始时间戳
- X
	- 时间步长(秒), 相当于一次性密码变更, 默认为30

公共参数:

- HMAC-based
	- 使用HMAC算法进行Hash
		- HMAC-SHA1
		- HMAC-SHA256
		- HMAC-SHA512

#### Truncate截取算法:

1. 取HMAC结果(20 byte)的最后一个字符的低4位`offset num`
2. `offset num` % 16 计算出字符截取的初始位置`offset`
3. HMAC结果的第`offset`开始, 取4个byte组成一个32位int

#### Digits参数
其中算法还有一个参数`Digits`, 代表验证码的位数

默认为6, 或者为8

### 秘钥URL
GoogleAuthenticator添加秘钥时, 可扫描二维码添加, 其中二维码为一条链接(`otpauth://TYPE/LABEL?PARAMETERS`)

例如: otpauth://totp/Example:alice@google.com?secret=JBSWY3DPEHPK3PXP&issuer=Example

参数说明:

- TYPE
	- 秘钥计算的类型, `hotp`或`totp`
- Label
	- 用于标识账号, 包括了账号名和标识内容
	- `label = accountname / issuer (“:” / “%3A”) *”%20” accountname`
- PARAMETERS(URI query参数)
	- Secret
		- 秘钥, base32编码
	- Issuer
		- 谁提供者此秘钥, 就是标识属于那个网站的验证
	- Algorithm(可选)
		- HMAC使用哪种HASH算法
			- SHA1(默认)
			- SHA256
			- SHA512
	- Digits(可选)
		- 验证码位数
			- 6(默认)
			- 8
	- Counter(当type为hotp时为必需)
		- 计算器初始值
	- Period(当type为totp时为才需要, 可选)
		- 时间步长(秒)
			- 30(默认)

对于GoogleAuthenticator, 很多参数会忽略掉, 均为其默认值

- Algorithm(SHA1)
- Digits(6)
- Period(30)

### 在实现HOTP时遇到的一些问题(应该比较多人遇到吧)
- 计算出的时间点需要转为8 byte数据
- 输入的秘钥需要Base32解码之后, 才能进行作为秘钥传入HMAC
- Truncate函数最后取的4 byte数据, 需要转为32位的int

### 参考
-  HOTP: An HMAC-Based One-Time Password Algorithm(rfc4226): <https://tools.ietf.org/html/rfc4226>
- Key Uri Format: <https://github.com/google/google-authenticator/wiki/Key-Uri-Format>
- HOTP和TOTP算法图解: <https://www.jianshu.com/p/a7b900e8e50a>
