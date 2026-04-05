---
title: Node.js的BT下载
hugo.Data: 2016-01-25
date: 2016-01-25
draft: false
tags:
categories:
  - node.js
---

近来想用Node.js做一个BT下载的程序，于是研究了一下BT下载相关的知识。

# 一、BT文件解析
	用了那么久的bt下载，都知道下载前，都是先解析种子文件(.torrent)的吧。
	
种子文件使用的是`BEncode(B编码)`保存数据，这种编码有四种数据结构：
### 1.string(字符串)
string的编码为 `<string length>:<string>`

	例: 7:example, 表示字符串 'example'

### 2.integer(整数)
integer的编码为 `i<integer>e`
	
	例: i123456e, 表示整数 123456

### 3.list(列表)
list的编码为 `l<BEncode>e`
	
	例: l6:stringi123ee, 表示数组 ['string', 123]

### 4.dictionary(字典)
dictionary的编码为 `d<BEcode的string><BEncode>e`,即`d<key><value>e`,key为BEcode的string,value为BEncode

	例:d3:key5:value4:testi123ee, 表示{key:'value', test:123}

> 注: list和dictionary可以相互嵌套


解析基本思路，dictionary的key肯定为字符串，value和list的元素为任意类型，使用递归思路解析比较简单。

> 附上一份自己写的简单解析代码: [node.js简单解析bencode](https://github.com/skytoup/node.js-BEncode)
	
# 二、BT文件结构
参考别人的文章: [torrent文件分析](http://www.cnblogs.com/hnrainll/archive/2011/07/21/2112809.html)

# 三、BT协议
还是参考别人的文章: 

0. [BT（带中心Tracker）通信协议的分析]( http://blog.chinaunix.net/uid-26548237-id-3056731.html)
0. [BitTorrent协议规范](http://blog.chinaunix.net/uid-14408083-id-2814554.html)
0. [常见P2P协议之BitTorrent 分析](http://blog.csdn.net/wengpingbo/article/details/9174363)

		发现BT协议还是挺复杂的,下载的代码一时半刻写不出来。
		
# 番外篇
## 一、 关于info_hash
网上的资料比较少，搜了好久，发现很多都是说得不是很清楚。

1.BT协议中的info_hash
> info_hash是种子解析后的对象的info节点中的pieces，其长度为20的倍数，每20个字节对应一块文件。在BT协议中，下载文件需要一块一块下载，下载那一块，就是看你传过去的info_hash。
>
> 但是BT文件里面的部分数据是ASCII码，不能直接传输过去，需要经过一定的编码。编码很简单，凡是ASCII码的数字、字母，都按数字、字母传输，其余的以它的16进制前面加一个`%`符号传输。
> 
> 例如: ASCII编码 `6D8875` 应该为 `m%88u`

2.BT种子转磁力链接的info_hash
> 经过各种搜索、测试，最后看源码发现，BT种子是如何转磁力链接。
> 首先把种子的info节点以BEcode编码，将其sha1散列，最后把得到的结果拼接到这里的末尾: `magnet:?xt=urn:btih:`。多个文件的情况，好像是用`&`符号拼接每一个文件。
> 
	原本想做一个测试测试的，谁知道node.js用buffer读取bt的部分string后，length就变了......

## 二、node.js的BT下载库 
找了好久, 终于发现了一个node.js的BT下载库, 功能还是挺强大的，貌似还有其它的功能。

上地址: [webtorrent](https://github.com/feross/webtorrent)

> 官方文档写得挺详细的，而且这个库简单易用。至于还有其它什么功能我也不太了解了。


## 文章参考
0. [torrent文件分析](http://www.cnblogs.com/hnrainll/archive/2011/07/21/2112809.html)
0. [BT（带中心Tracker）通信协议的分析]( http://blog.chinaunix.net/uid-26548237-id-3056731.html)
0. [BitTorrent协议规范](http://blog.chinaunix.net/uid-14408083-id-2814554.html)
0. [常见P2P协议之BitTorrent 分析](http://blog.csdn.net/wengpingbo/article/details/9174363)