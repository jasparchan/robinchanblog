---
title: iOS 设置单个页面支持横竖屏
date: 2016-12-23 14:17:55
tags:
---

因为项目有VR页面，需要横屏模式。而非VR页面没有做横屏适配，故只支持竖屏。
<!-- more -->
### 对横竖屏的前期准备
* 首先要在工程设置设备支持的方向，路径project -> tagrgets -> general
  ![](http://image.robinchan.cn/iOSSupportedInterfaceOrientation.png)
  
* 要控制某个 ViewController 支持横竖屏的方法需要在此 ViewController 的 rootViewController 里面重写。present 出来的 ViewController 也一样。   

```
//是否支持自动切换横竖屏
- (BOOL)shouldAutorotate NS_AVAILABLE_IOS(6_0) __TVOS_PROHIBITED;
//支持的方向
- (UIInterfaceOrientationMask)supportedInterfaceOrientations NS_AVAILABLE_IOS(6_0) __TVOS_PROHIBITED;
// Returns interface orientation masks.
//当返回这个页面时的优先方向
- (UIInterfaceOrientation)preferredInterfaceOrientationForPresentation NS_AVAILABLE_IOS(6_0) __TVOS_PROHIBITED;
```

### 要注意的地方
我的需求是：给需要横竖屏的页面支持，而其他默认不支持。
* 如果 rootViewController 为 UIViewController，直接重写，present 出来的 ViewController 也要相应重写这两个方法

```
- (BOOL)shouldAutorotate{
    return NO;
}
#if __IPHONE_OS_VERSION_MAX_ALLOWED < __IPHONE_9_0
- (NSUInteger)supportedInterfaceOrientations
#else
- (UIInterfaceOrientationMask)supportedInterfaceOrientations
#endif
{
    return UIInterfaceOrientationMaskPortraitUpsideDown;
}
```

* 如果 rootViewController 为 UINavigationController

```
-(BOOL)shouldAutorotate
{
    UIViewController *viewController = self.topViewController;
    if ([viewController isKindOfClass:[#你想要支持的 ViewController# class]]) {
        return YES;
    }else {
        return NO;
    }
}

#if __IPHONE_OS_VERSION_MAX_ALLOWED < __IPHONE_9_0
- (NSUInteger)supportedInterfaceOrientations
#else
- (UIInterfaceOrientationMask)supportedInterfaceOrientations
#endif
{
    UIViewController *viewController = self.topViewController;
    if ([viewController isKindOfClass:[#你想要支持的 ViewController# class]]) {
        return UIInterfaceOrientationMaskAllButUpsideDown;
    }else{
        return UIInterfaceOrientationMaskPortrait;
    }
}
```

* 如果 rootViewController 为 UITabBarController，跟 UINavigationController 一样。我的UITabBarController的viewControllers都是UINavigationController，所以代码如下：

```
-(BOOL)shouldAutorotate
{
	 UINavigationController *nav = (UINavigationController *)self.selectedViewController;
    UIViewController *viewController = nav.topViewController;
    if ([viewController isKindOfClass:[#你想要支持的 ViewController# class]]) {
        return YES;
    }else {
        return NO;
    }
}

#if __IPHONE_OS_VERSION_MAX_ALLOWED < __IPHONE_9_0
- (NSUInteger)supportedInterfaceOrientations
#else
- (UIInterfaceOrientationMask)supportedInterfaceOrientations
#endif
{
	 UINavigationController *nav = (UINavigationController *)self.selectedViewController;
    UIViewController *viewController = nav.topViewController;
    if ([viewController isKindOfClass:[#你想要支持的 ViewController# class]]) {
        return UIInterfaceOrientationMaskAllButUpsideDown;
    }else{
        return UIInterfaceOrientationMaskPortrait;
    }
}
```

end