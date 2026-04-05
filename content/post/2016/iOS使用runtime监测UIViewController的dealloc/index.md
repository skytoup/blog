---
title: iOS使用runtime监测UIViewController的dealloc
hugo.Data: 2016-02-02
date: 2016-02-02
draft: false
tags:
categories:
  - iOS
---

平时在iOS开发的时候，很多情况会导致内存泄露。有时候因为循环引用，导致了`UIViewController`不回收，还有其它好多原因。一般检测内存泄露都是使用`Xcode`的`Instruments`工具。但是这个工具有点复杂，新手入门还是有点难度。所以想到了使用runtime替换`UIViewController`的`-(void)dealloc:`方法的实现，检测`ViewController`是否被释放，从而知道`ViewController`里面有没有内存泄露。

	runtime的相关细节就不说了，不理解也能用，复制放到项目里面就好了。
	
# 一、思路
1. 需要被替换的方法是`UIViewController`的`-(void)dealloc:`，所以新建一个`UIViewController`的`Category`，在其`+(void)load`里面执行方法替换。
2. 替换的新方法里面做一个简单地log一下当前`UIViewController`的类名就好了就好了，即当UIViewController被回收的时候，log其类名。

# 二、实现
1.新建一个UIViewController的`Category`，编写新的dealloc方法。代码如下(可自行修改):

```objective-c
- (void)skyLogInDealloc {
    printf("\n");
    NSLog(@"-------------start-------------");
    NSLog(@"Dealloc : %@", NSStringFromClass([self class]));
    NSLog(@"--------------end--------------");
    printf("\n");
    [self skyLogInDealloc];
}
```

2.重写`+(void)onLoad:`方法。代码如下:

```objective-c
+ (void)load {
    [super load];
    
    SEL originSEL  = NSSelectorFromString(@"dealloc");
    SEL swapSEL = @selector(skyLogInDealloc);
    
    Method originMethod = class_getInstanceMethod(self, originSEL);
    Method swapMethod = class_getInstanceMethod(self, swapSEL);
    
    IMP originIMP = method_getImplementation(originMethod);
    IMP swapIMP = method_getImplementation(swapMethod);

    BOOL didAddMethod = class_addMethod(self, originSEL, swapIMP, method_getTypeEncoding(originMethod));
    
    if(didAddMethod) {
        class_replaceMethod(self, swapSEL, originIMP, method_getTypeEncoding(originMethod));
    } else {
        method_exchangeImplementations(originMethod, swapMethod);
    }
}
```

完整的m文件实现代码

```objective-c
#if DEBUG
+ (void)load {
    [super load];

    SEL originSEL  = NSSelectorFromString(@"dealloc");
    SEL swapSEL = @selector(skyLogInDealloc);
    
    Method originMethod = class_getInstanceMethod(self, originSEL);
    Method swapMethod = class_getInstanceMethod(self, swapSEL);
    
    IMP originIMP = method_getImplementation(originMethod);
    IMP swapIMP = method_getImplementation(swapMethod);

    BOOL didAddMethod = class_addMethod(self, originSEL, swapIMP, method_getTypeEncoding(originMethod));
    
    if(didAddMethod) {
        class_replaceMethod(self, swapSEL, originIMP, method_getTypeEncoding(originMethod));
    } else {
        method_exchangeImplementations(originMethod, swapMethod);
    }
}

- (void)skyLogInDealloc {
    printf("\n");
    NSLog(@"-------------start-------------");
    NSLog(@"Dealloc : %@", NSStringFromClass([self class]));
    NSLog(@"--------------end--------------");
    printf("\n");
    [self skyLogInDealloc];
}
#endif
```

# 三、部分细节
1. 在ARC下，使用`@selector(dealloc:)`会报错，所以只好这样子获取它的SEL: `NSSelectorFromString(@"dealloc")`
2. `DEBUG`是一个宏，当构建项目使用Debug的时候，其值会为YES，当使用Release的时候，其值会是NO。加上去就是为了发布的时候，也不需要担心忘记将其移除功能。

附上github地址: <https://github.com/skytoup/SkyLogInDealloc>