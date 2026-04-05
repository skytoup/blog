---
title: 便捷存储简单数据SimpleStoreData
hugo.Data: 2020-03-18
date: 2020-03-18
draft: false
tags:
categories:
  - iOS
---
> 为啥不叫`SimpleStore`??? 因为`Cocoapods`已有同名, 临时在后面加个`Data`😭, 实际使用还是`SimpleStore`
> 下文`SimpleStore`和`SimpleStoreData`指的为同一样东西

两三年前, 公司需要`Swift`开发`App`, 从那时刚开始接触学习, 发现`struct`的一些特性可以结合`UserDefault`简化一些业务数据存储和读写, 于是就写出了`SimpleStore`的雏形.

随着`Swift 5.1`的发布, 发布了一个`PropertyWrapper`的新特性, 尝试把这个特性加到`SimpleStore`的雏形里面, 发现使用起来还是更方便. 顺便还升级了功能, 一个`StoreItem`由原本只支持单一类型存储, 改为支持多类型.

最近对`SimpleStore`的雏形进行了一番思考, 对其再次升级, 最后整理开源了`SimpleStore`.

### 设计思路
- 核心
	`SimpleStoreItem`修改后, 触发`SimpleStore`进行对数据存储
- 基本结构
	- SimpleStore: 管理和存储`SimpleStoreItem`
	- SimpleStoreItem: 存放数据
	- Mapper: 辅助数据间转换(非必须)
- 扩展性
	- 可实现`SimpleStore`协议可配合`SimpleStoreItem`扩展更多存储方式(目前只有`UserDefault`)
	- `SimpleStoreDictItem`实现了`SimpleStoreItem`协议, 通过`Mapper`提供`Item`和`dict`的转换, 为中间转存提供方便, 易于扩展更多存储方式

### 用法

场景: 存储登录用户的数据(存储到UserDefault)

定义一个用户性别的枚举, 因为`UserDefault`不支持直接存储`enum`, 所以需要实现`UDCoding`

`UserDefault`支持的存储类型:

 - String
 - Int
 - Float
 - Double
 - Bool
 - Data
 - Date
 - URL

```Swift
enum Gender: Int {
    case unknown = 0
    case male
    case female
}
/// 对于`UserDefault`不支持的存储类型提供数据转换
extension Gender: UDCoding {
    func toUDAny() -> Any {
        rawValue
    }
    
    static func fromUDAny(_ any: Any?) -> Gender? {
        guard let any = any as? Int else {
            return nil
        }
        return Gender(rawValue: any)
    }
}
```

定义存储用户数据的字段(**重点提示: SimpleStoreItem必须使用struct实现**), 字段使用`UDMapperParam`属性包装定义, 再实现`mapValue`方法进行数据映射

```Swift
/// **重点提示: SimpleStoreItem必须使用struct实现**
struct UserStoreItem: SimpleStoreUDItem {
    @UDMapperParam(key: "name", default: "") var name
    @UDMapperParam(key: "age", default: 0) var age
    @UDMapperParam(key: "gender", default: Gender.unknown) var gender
    
    mutating func mapValue<Mapper>(_ mapper: inout Mapper) where Mapper : DictMapper, UserStoreItem.Key == Mapper.Key, UserStoreItem.Value == Mapper.Value {
        mapper <- _name
        mapper <- _age
        mapper <- _gender
    }
}
```

定义管理`StoreItem`的管理类(此处简单使用单例, 可根据自己的业务进行调整, `manager`也可定义更多业务方法)

```Swift
class UserManager: SimpleStoreUD<UserStoreItem> {
    static let shared = UserManager(udKey: "__User__")
    
    // 这里可以添加一些业务方法
}
```

使用演示, 修改item数据即可存自动储, 无需其它调用方法

```Swift
var userM = UserManager.shared
// 清空之前的数据
userM.ud.set(nil, forKey: userM.udKey)

// 提示: 一次性更新多个字段最好是使用方法`batchUpdate`
userM[\.name] = "sky"
print("第一次修改 \(userM.ud.dictionary(forKey: userM.udKey) ?? [:])")
// 第一次修改 ["gender": 0, "age": 0, "name": sky]

userM[\.age] = 18
print("第二次修改 \(userM.ud.dictionary(forKey: userM.udKey) ?? [:])")
// 第二次修改 ["name": sky, "age": 18, "gender": 0]

userM[\.gender] = .male
print("第三次修改 \(userM.ud.dictionary(forKey: userM.udKey) ?? [:])")
// 第三次修改 ["age": 18, "name": sky, "gender": 1]


// 批量修改字段再存储
userM.batchUpdate { item in
    item.name = "yks"
    item.age = 81
    item.gender = .female
}
print("batchUpdate \(userM.ud.dictionary(forKey: userM.udKey) ?? [:])")
// batchUpdate ["age": 81, "name": yks, "gender": 2]

// 相当于重置数据
userM.item = UserStoreItem()
print("重置数据 \(userM.ud.dictionary(forKey: userM.udKey) ?? [:])")
// 重置数据 ["age": 0, "name": , "gender": 0]
```

### 详情
详情请参考github地址: <https://github.com/skytoup/SimpleStoreData>