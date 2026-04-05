---
title: NSHashTable和NSMapTable
hugo.Data: 2018-04-28
date: 2018-04-28
draft: false
tags:
categories:
  - 
---

原文来自Apple官方文档: 
- NSHashTable: <https://developer.apple.com/documentation/foundation/nshashtable?language=objc>
- NSMapTable: <https://developer.apple.com/documentation/foundation/nsmaptable?language=objc>
- NSPointerFunctions: <https://developer.apple.com/documentation/foundation/nspointerfunctions?language=objc>
- NSPointerFunctionsOptions: <https://developer.apple.com/documentation/foundation/nspointerfunctionsoptions?language=objc>

# NSHashTable
一个类似set的集合，但是它有更多的内存语义(*更自由定义集合元素的内存如何管理)。

## 概述
NSHashTable和NSSet不同之处：
	- 它可以对它的元素保持弱引用。
	- 它的元素可以在输入时拷贝副本 或 使用指针地址对比相同和哈希。
	- 他可以存储任意指针(它的元素不限制是对象[*应该是指NSObject])

你可以配置一个NSHashTable实例用于任意的指针，不限于是对象，显然你可以能够使用把C函数转为 void* 指针。它的基本API(如addObject:)将会不对非对象的指针进行类型转换。

因为它的配置，NSHashTable不是一个Set，应为它们有不同之处(比如，如果配置了使用对比指针地址，则两个isEqual:的字符串都将被添加)

当配置哈希表时，请注意只有在NSHashTableOptions列出的配置才能使API正常使用--包括复制、归档和快速遍历。而其它NSPointerFunctions配置使用于某些配置，例如保持任意指针，单并非所有组合的配置都是有效的。对于谋学组合，哈希表可能无法正常工作，甚至无法正常初始化。


# NSMapTable
一个类似字典的集合，但是它有更多的内存语义(*更自由定义集合元素的内存如何管理)。

## 概述
NSMapTable和NSDictionary不同之处：
	- Key/Value 是可以配置保持弱引用，当对象被回收时就会被移除。
	- Key/Value可以在输入时拷贝副本 或 使用指针地址对比相同和哈希。
	- 他可以存储任意指针(它的元素不限制是对象[*应该是指NSObject])

你可以配置一个NSMapTable实例去操作任意指针，不限于对象，显然你可以能够使用把C函数转为 void* 指针。它的基本API(如addObject:forKey:)将会不对非对象的指针进行类型转换。

当配置MapTable时，请注意只有在NSMapTableOptions列出的配置才能使API正常使用--包括复制、归档和快速遍历。而其它NSPointerFunctions配置使用于某些配置，例如保持任意指针，单并非所有组合的配置都是有效的。对于谋学组合，哈希表可能无法正常工作，甚至无法正常初始化。


# NSPointerFunctions
一个NSPointerFunctions实例定义了调出函数用于管理如何保存指针。

## 概述
NSPointerFunctions实例所指定的函数被分为两个簇——定义一些“特性”的函数，如“object”或“C-string”，以及描述内存管理问题的函数，如内存分配函数。对于常见的特性和内存管理器的选择有一些常量(参见内存和特性选项)。

NSHashTable，NSMapTable 和 NSPointerArray使用一个NSPointerFunctions对象定义指针的获取和存储。但是请注意，并不是所有的特性和内存管理的组合对这些集合都有效。指针集合对象在输出输入时复制NSPointerFunctions对象，	所以不能把NSPointerFunctions子类化。


# NSPointerFunctionsOptions
对NSPointerFunction对象定义内存和特性的配置

## 概述
当指定一个值时，你只能使用内存选项的其中一个。

## 配置选项
### Memory Options(这些选项是互斥的)
- NSPointerFunctionsMachVirtualMemory
	- 使用Mach内存
- NSPointerFunctionsMallocMemory
	- 删除使用free()，复制使用calloc()
- NSPointerFunctionsOpaqueMemory
   - 当指针被删除时，不要采取任何操作
- NSPointerFunctionsStrongMemory
	- 使用强引用来存储，在复制时使用垃圾回收
- NSPointerFunctionsWeakMemory
	- 对ARC和GC的读写使用弱引用。使用NSPointerFunctionsWeakMemory对象引用被释放后将在将会返回NULL

### Personality Options(这些选项是互斥的)
- NSPointerFunctionsCStringPersonality
	- 使用字符串的哈希 和 比对；C-string使用‘%s’
- NSPointerFunctionsIntegerPersonality
	- 使用未位移的值用于哈希和比对
- NSPointerFunctionsObjectPersonality
	- 使用哈希 和 isEqual方法对比是否相同，使用description方法显示详情
- NSPointerFunctionsObjectPointerPersonality
	- 使用移位指针进行哈希和直接对比相同，使用description方法显示详情
- NSPointerFunctionsOpaquePersonality
	- 使用移位指针进行哈希和直接对比相同
- NSPointerFunctionsStructPersonality
	- 使用内存的哈希 和 memcmp(使用sizeFunction).

