---
title: ShortUUID - UUID生成对应缩短的字符
hugo.Data: 2021-06-27
date: 2021-06-27
draft: false
tags:
categories:
  - 
---
> 这个库最开始是`python`的版本<https://github.com/skorokithakis/shortuuid>
> 
> 后来还有一个`Go`的版本<https://github.com/lithammer/shortuuid>
> 

UUID字符长度为32(去掉`-`), ShortUUID默认生成字符长度为20, 所以在以前在`python`项目中作为短的唯一标识生成, 趁着有空试试撸一个`swift`的版本<https://github.com/skytoup/shortuuid>。

刚开始分析`python`版的源码, 发现设计得还挺简单的。所有算法逻辑都在一个文件, 源码也就`141行`。

## UUID简介

全称`Universally Unique Identifier`, 是通用唯一识别码, 由128 bit(16 byte)的数值组成。

具体介绍: <https://baike.baidu.com/item/UUID/5921266?fr=aladdin>

## 算法大概流程

`ShortUUID`包含一组字符`Alphabet`用于生成`短UUID`的字符, 以`ascii`码小到大排序并且不重复。

- `UUID`转(encode)`short UUID`
	- 把`UUID`作为数据不断整除`Alphabet`的长度, 并以`余数`作为索引选取`Alphabet`的字符, 直至被除数为0, 最后组成的字符翻转。
	- 伪代码

		```
		while UUID > 0:
			UUID, rem = UUID / Alphabet.length, UUID % Alphabet.length
			`short uuid` += `Alphabet`[rem]

		`short uuid`.reverse()
		```

- `short UUID`转(decode)`UUID`
	- 就是把`encode`的算发反转, `UUID`(初始为0)作为数据乘以`Alphabet`的长度, 再加上`short UUID`对应`Alphabet`的索引位置。
	- 伪代码

		```
		UUID = 0
		for char in `short UUID`:
			UUID * `Alphabet`.length + `Alphabet`.indexOf(char)
		```

## 看似简单, 实现起来还是有些难度

### 大数运算

`UUID`为`128 bit`的数据, `python`自带大数运算功能, 但`swift`的最大位数`Int`也只有`UInt64`和`Int64`, 即大**正**整数运算只能用进行`64 bit`进行运算。而且在标准库中暂未发现有大数运算的相关支持库, 所以只能写一个。

这个算法仅用到`128 bit`的运算有
	
- `除并且求余`
- `乘`
- `加`
	
主要实现思路是只拆分为`32 bit`的数据进行分别运算, 最后把拆分的运算结果组合为`128 bit`运算结果(具体实现看源码)

### UUID5

`python`标准库有相关实现, 但`swift`没有。实现也比较简单, 主要流程是`namespace`对应`UUID bytes`拼上`input string`再进行`SHA1`(结果为`160 bit`), 取前`128 bit`修改部分数据即可。

**相关链接**

- python uuid源码: <https://github.com/python/cpython/blob/main/Lib/uuid.py>
- oc的uuid5源码: <https://gist.github.com/eliburke/1a55ed616bb15a7f908b>
