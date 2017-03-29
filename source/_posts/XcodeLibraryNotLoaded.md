title: 真机调试APP时报Library Not Loaded错误
date: 2016-03-08 17:59:55
tags: iOS 
---
原因：我在搭建[Jenkins](http://robinchan.cn/2016/02/29/JenkinsCI/)的时候，把证书设置为始终信任，如下图。
![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/KeychainSetting1.png)
我的项目使用了`Cocoapods`，并且在Podfile加入`use_frameworks!`，然后在真机调试的时候就会出现以下错误（模拟器并不会）。
```
dyld: Library not loaded: @rpath/AFNetworking.framework/AFNetworking
  Referenced from: /var/mobile/Containers/Bundle/Application/BD8D7CF1-5E24-4B92-A300-70E28C82C1BA/text1.app/text1
  Reason: no suitable image found.  Did find:
	/private/var/mobile/Containers/Bundle/Application/BD8D7CF1-5E24-4B92-A300-70E28C82C1BA/text1.app/Frameworks/AFNetworking.framework/AFNetworking: mmap() errno=1 validating first page of '/private/var/mobile/Containers/Bundle/Application/BD8D7CF1-5E24-4B92-A300-70E28C82C1BA/text1.app/Frameworks/AFNetworking.framework/AFNetworking'
	/private/var/mobile/Containers/Bundle/Application/BD8D7CF1-5E24-4B92-A300-70E28C82C1BA/text1.app/Frameworks/AFNetworking.framework/AFNetworking: mmap() errno=1 validating first page of '/private/var/mobile/Containers/Bundle/Application/BD8D7CF1-5E24-4B92-A300-70E28C82C1BA/text1.app/Frameworks/AFNetworking.framework/AFNetworking'
	/private/var/mobile/Containers/Bundle/Application/BD8D7CF1-5E24-4B92-A300-70E28C82C1BA/text1.app/Frameworks/AFNetworking.framework/AFNetworking: mmap() errno=1 validating first page of '/private/var/mobile/Containers/Bundle/Application/BD8D7CF1-5E24-4B92-A300-70E28C82C1BA/text1.app/Frameworks/AFNetworking.framework/AFNetworking'
(lldb) 
```
![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/XcodeError.png)
谷歌了半天，试了各种方法。然后找到了简书上面的帖子[XCode真机调试APP时报dyld: Library not loaded: @rpath/XXX等错误](http://www.jianshu.com/p/b6b2fc31eb2a)
然后修改回来，瞬间天晴，谢谢[黑暗中的孤影](http://www.jianshu.com/users/ba6dc2f48796/latest_articles)
![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/KeychainSetting2.png)