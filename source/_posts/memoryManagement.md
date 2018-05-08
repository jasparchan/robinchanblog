---
title: 内存管理的一些事
date: 2017-09-25 20:35:33
tags: iOS 内存
---
## 内存管理的思考方式
* 自己生成的对象，自己持有
* 非自己生成的对象，自己也能持有
* 自己不再需要持有对象时，自己释放
* 非自己持有的对象无法自己释放

> 无论ARC或者MRC都遵循以上思考方式，只是ARC的时候编辑器帮我做了这些事情

### 自己生成的对象，自己持有
四个关键词alloc、new、copy、mutableCopy，若自身使用这些关键词生成对象，则自身持有这个对象
### 非自己生成的对象，自己也能持有
并没有通过（alloc、new、copy、mutableCopy）四个关键词来生成的对象属于非自己生成的对象。例如NSMutableArray通过类方法array生成对象。若自己仍想持有对象，则需要使用retain方法。
### 自己不再需要持有对象时，自己释放
当自己不再需要持有时，发送release消息
### 非自己持有的对象无法自己释放
自己释放过对象，无法再次释放
非自己持有的对象，也无法释放

## 循环引用问题
我们都知道，对象的生命周期交给了一个叫做“引用计数”的东西。当两个对象或者多个对象之间依次持有，形成一个环状时，就造成了循环引用问题。解决方案就是在合理的位置主动断开环中的一个引用，使得对象得以回收。但有时很难发现，或者很难确定在哪断开。最常见的方法就是使用弱引用（weak reference），弱引用持有对象，但并不增加引用计数，这样就避免了循环引用的产生。在delegate模式和block中常用弱引用。
## ARC内存管理
### 本质
> Automatic Reference Counting (ARC) is a compiler-level feature that simplifies the process of managing object lifetimes (memory management) in Cocoa applications.

### 开启和关闭
如果需要对特定文件开启或关闭ARC，可以在工程选项中选择Targets -> Compile Phases -> Compile Sources，在里面找到对应文件，添加flag:
打开ARC：-fobjc-arc
关闭ARC：-fno-objc-arc

### strong 、weak、unsafe_unretained、autoreleasing
#### __strong
定义property时的"strong",如果在声明引用时不加修饰符，那么引用将默认是强引用。强指针指向一个对象，这个对象的引用计数就加1
#### __weak
weak修饰的指针没有引起对象内部的引用计数器的变化，因此，weak修饰的指针常用于打破循环引用或者修饰UI控件。
#### __unsafe_unretained
unsafe_unretained作用需要和weak进行对比，它也不会引起对象的内部引用计数器的变化，但是，当其指向的对象被销毁时unsafr_unretained修饰的指针不会置为nil。不安全，不用。
#### __autoreleasing
表示在autorelease pool中自动释放对象的引用，和MRC时代autorelease的用法相同。
main函数里面的
```
int main(int argc, char * argv[]) {
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```
### Core Foundation对象的内存管理
ARC能处理iOS开发大部分的内存管理问题，除了Core Foundation对象。
一般来说有CFRetain喝CFRelease两种方法，可以直观认为与Objective-C对象的retain和release方法等价
对于Core Foundation与objective-cObject进行交换时，需要用到的ARC管理机制有：
(__bridge_transfer<NSType>) op oralternatively CFBridgingRelease(op) iSUSEd to consume a retain-count of a CFTypeRef whiletransferring it over to ARC. This could also be represented by id someObj =(__bridge <NSType>) op; CFRelease(op);
(__bridge_retained<CFType>) op oralternatively CFBridgingRetain(op) isused to hand an NSObject overto CF-land while giving it a +1 retain count. You should handle a CFTypeRefyoucreate this way the same as you would handle a result of CFStringCreateCopy().This could also be represented by CFRetain((__bridge CFType)op); CFTypeRef someTypeRef =(__bridge CFType)op;
__bridge justcasts between pointer-land and Objective-C object-land. If you have noinclination to use the conversions above, use this one.


