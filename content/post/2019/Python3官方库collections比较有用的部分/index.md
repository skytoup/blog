---
title: Python3官方库collections比较有用的部分
hugo.Data: 2019-07-21
date: 2019-07-21
draft: false
tags:
categories:
  - python3 python
---


### collections.Counter
1. 简介
	- `Counter`是`dict`的子类, 用于可`hash`的对象计数
	- `key`按`dict`的`key`排序, 计数的值按`dict`的`value`排序
	- 计数的值为`整数`, 可为`正负数`和`0`

2. 示例

```python3
from collections import Counter


# 初始化, 可传 [None, Iterable, Mapping]
counter = Counter()  # None
counter = Counter('12345')  # Iterable, 统计每个元素频率
counter = Counter({'a': 1, 'b': 2})  # Mapping
counter = Counter(a=1, b=2)  # Mapping

# 没有赋值过的元素不会触发KeyError
counter['c']  # 0
counter['c'] += 1

# 也能移除元素(真实移除), 然后计数归0
counter['c'] = 100
del counter['c']
counter['c']  # 0

# 获取计数最大的元素排序list
c = Counter('1122234444449999999999')
c.most_common()  # [('9', 10), ('4', 6), ('2', 3), ('1', 2), ('3', 1)]
# 获取前n个
c.most_common(3)  # [('9', 10), ('4', 6), ('2', 3)]

# 比较特殊的操作
+c  # 去除计数为0和负数的元素
-c  # 去除计数为0和正数的元素, 并把负数的元素取绝对值
```

### collections.defaultdict
1. 简介
	- `dict`的子类
	- 提供`默认值构造器`传到内部, 可为不存在的key设置默认值
	- `默认值构造器`为一个无参可调用的对象, 返回默认的数据
	
2. 示例

```python3
from collections import defaultdict


df_dict = defaultdict(list)
df_dict['a'].append(1)
df_dict['a'].append(2)
df_dict['b'].append(3)
df_dict['b'].append(4)  # {'a': [1, 2], 'b': [3, 4]}

df_dict = defaultdict(lambda: list())  # 使用lambda
```