---
title: iOS通用链接 By Winson
date: 2017-07-26 13:44:37
tags:
---
![](http://upload-images.jianshu.io/upload_images/6067780-bfbf1d20b36064e7.gif?imageMogr2/auto-orient/strip)

[官方文档 ](https://developer.apple.com/library/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1)

> Q1：我的应用为何要使用通用链接？

我们的应用有很多分享到微信、QQ等第三方平台的内容，使用过程中，为了让用户在分享到微信的网页里面点击某个链接或者打开按钮直接转到我们的app，盯上了这个iOS 9之后才有的系统级的功能，他就是 Universal Links。虽然说通用链接并不是专门为这种设计的，但是要实现我上面说的 微信跳转到你的app ，它还是很方便的。

> Q2：为何不用scheme？

scheme的方式是不允许从微信或者其他app跳转到你的app的，除非微信或者其他app的白名单里面有你的app的scheme，这好像也不是好多文章里面说的微信禁用了scheme，微信都不知道你的scheme怎么给你跳转到你的应用呢 。（个人理解）

我把它分成三部分吧（Developer Settings）
1. [**Developer Settings** （开发者中心配置）](#1. Developer Settings)
2. [**HTTPS Settings**（服务器配置）](#2. HTTPS Settings)
3. [**Xcode Settings**（Xcode配置）](#3. Xcode Settings)

<!-- more -->
<span id="1. Developer Settings"></span>
## Developer Settings
首先，需要在开发者中心开启Associated Domains功能，具体操作是：

在 **App IDs** 选择你要修改的 **App ID** ；
![](http://upload-images.jianshu.io/upload_images/6067780-a187a924f6edd604.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击要修改的 **App ID**，在列表中勾选 **Associated Domains** ；
![](http://upload-images.jianshu.io/upload_images/6067780-e5aab7f027006d2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在弹出框点确定，这个警告是告诉你，你如果启用该功能，就相当于编辑了这个 **App ID**，那么你现有的用该 **App ID** 生成的描述文件就得重新生成并导入至 **Xcode** 中了；
![](http://upload-images.jianshu.io/upload_images/6067780-aeda0e60f1172991.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击确定后，你会发现 **Associated Domains** 可用了；
![](http://upload-images.jianshu.io/upload_images/6067780-16b4c4c8a3a0dce4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击描述文件，发现失效了，那是因为你编辑过生成该描述文件用到的 **App ID**，不急，编辑它就是了；
![](http://upload-images.jianshu.io/upload_images/6067780-5f5701fbc4343255.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
编辑描述文件，只需要重新勾选 **App ID** 即可，然后保存的描述文件又变成有效的了。下图注释中说的很明确了，**Download** 该描述文件，双击安装即可。
![](http://upload-images.jianshu.io/upload_images/6067780-57223885809e51b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
开发跟发布的描述文件都重新生成并下载安装之后，开发者中心的配置就完成了。

<span id="2. HTTPS Settings"></span>
## HTTPS Settings
### 有一个注册的通过 **SSL** 访问的域名（**HTTPS**）
假设你的域名为 **domain** ，例如：*www.xxx.com* 或 *xxx.com* 或 *xxx.xxx.com* 都能当做是域名，具体看你后台怎么给你配，我这里称之为 **domain**，**domain** 代表上述那几种域名。


### 支持上传一个 **JSON** 文件到你的域名
用文本编辑器写一个 **JSON** ，该 **JSON** 的格式是：
```
{
    "applinks": {
        "apps": [],
        "details": [
            {
                "appID": "TeamID.BundleIdentifier",
                "paths": [ "*" ]
            }
        ]
    }
}
```
这个 **JSON** 最好去 [官方文档](https://developer.apple.com/library/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1) 去复制，我之前复制了一片博客上的，就是错的。
 上面的 **appID** 的组成结构是 **TeamID.BundleIdentifier**
1. **TeamID** 可以去开发者中心的 **Account -> Membership** 下去找。如图：
![](http://upload-images.jianshu.io/upload_images/6067780-eed9c6467802ecfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. **BundleIdentifier** 就是你应用的 **Bundle Identifier**，如图：
![](http://upload-images.jianshu.io/upload_images/6067780-ab9401bb05fc5711.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这个 **JSON** 其他 **key** 的作用我就不过多赘述了，具体看官方文档，** paths** 我这里是用了 **\***，代表支持该域名下的所有链接跳转至 **App**。
3. 编辑好该 **JSON** 后，保存，命名为：`apple-app-site-association`，注意，这里不能给该文件冠以 `.json`之类的后缀。如图：
![](http://upload-images.jianshu.io/upload_images/6067780-08ed38284d1f1fbc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. 将保存好的 **JSON** 文件 `apple-app-site-association`上传至HTTPS服务器域名的 **根目录** 或者根目录下的`.well-known`文件夹下。例如：https://domain/apple-app-site-association 或者 https://domain/.well-known/apple-app-site-association， 其中 **domain** 就是你的域名，上面已经概述过。


### 能通过 **GET** 请求访问该域名下的 **JSON**
上传好 **JSON** 文件后，最简单的办法是通过浏览器访问该文件，如果能得到该 **JSON** 内容，说明已经可以访问该文件。如图：
![我这浏览器装了json插件，没装的效果可能不是上图所示。](http://upload-images.jianshu.io/upload_images/6067780-744b731c22db8998.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
有的博客写可以去苹果的一个 [检测网站](https://search.developer.apple.com/appsearch-validation-tool/) 检测是否可用，我反正是每次都失败了，纠结了我好久。其实，用浏览器能访问到该json说明也是成功了，别纠结。
至此，web的配置就完成了。

    
<span id="3. Xcode Settings"></span>
## Xcode Settings
### 工程配置

**Targets -> Capabilities -> Associated Domains**
![](http://upload-images.jianshu.io/upload_images/6067780-200605e98ee85929.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这里的操作就是添加 **Domains**，具体写 **applinks:domain**，这里的 **domain** 跟上一步 **Web Settings** 里面的 **domain** 是一致的。
`假设你的域名为 domain ，例如：www.xxx.com 或 xxx.com 或 xxx.xxx.com 都能当做是域名，具体看你后台怎么给你配，我这里称之为 domain，domain 代表上述那几种域名。`
这里开启了 **Associated Domains**  功能后，你的工程会自动创建一个 **.entitlements** 文件，如图：
![](http://upload-images.jianshu.io/upload_images/6067780-2b8eb327d95c3ce8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
到这里的时候，你的通用链接基本打通了。你可以通过简单的方法来测试一下。
我的测试方法是：
 1. 删除已安装的app，重新安装；
 2. 待程序安装好后，打开备忘录，在备忘录里面随便输入一个带域名的链接，比如：https://domain/xxx， 点击右上角完成按钮；
 3. 长按该链接，如果已接通通用链接，底部弹出框会多出一栏，显示“在xxx中打开”，其中这个xxx就是你应用的名字。如图：
![](http://upload-images.jianshu.io/upload_images/6067780-c7569ec3df79fbd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明，已经接通了。
### AppDelegate.m 接收并处理回调
通过浏览器或者别的app里面的网页内点击你的通用链接跳转至你的app之后，会执行这个方法：
-application:continueUserActivity:restorationHandler:
以下是我的代码：
```
 - (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray * _Nullable))restorationHandler {
    if ([userActivity.activityType isEqualToString:NSUserActivityTypeBrowsingWeb]) {
        NSURL *webUrl = userActivity.webpageURL;
        NSLog(@"host = %@", webUrl.host);
        NSString *urlString = webUrl.absoluteString;
        NSArray *arr = [urlString componentsSeparatedByString:@"xxx"];
        if (arr.count == 2) {
            if ([[arr lastObject] length] > 0) {
                NSString *paraString = [arr lastObject];
                NSString *schemeStr = @"xxxxxxx";
                if ([paraString hasPrefix:schemeStr]) {
                    NSLog(@"接收到了");
                    NSLog(@"root = %@", self.window.rootViewController);
                    id rootVC = self.window.rootViewController;
                    if ([rootVC isKindOfClass:[LoginNVC class]]) {
                        NSLog(@"未登录，请先登录");
                    } else {
                        NSLog(@"已登录直接跳转至指定页面");
                        [self gotoDiffrentVCWithUrl:[NSURL URLWithString:paraString]];
                    }
                }
            }
        }
        return YES;
    } else {
        return NO;
    }
}
```
代码写的比较仓促，不用纠结，里面的关键key我也用xxx来代替了，主要看你后台的通用链接的参数啊什么的有没有指定规则吧，比如我这里都是遵循制定好的规则来跳指定页面的。

## 最重要的几点（坑😤）
### domain 不能跟你的网页的  host  相同
我配置好了之后，就来在后台写了一个网页，网页的 **host** 用的是 **domain**，网页里面加了几个超链接，超链接的地址的 **host** 用的是 **domain**，然后分享到微信，在微信打开的网页里面点击我写好的几个超链接，发现，天哪，居然直接在微信的网页内继续打开了，并没有跳转到我的 **app**。用 **Safari ** 打开，居然也是在 **Safari ** 中继续打开网页了，没有跳转至我的 **app**。崩溃了😖……然后纠结了好久，是我哪里没配置好吗，是不是文件的 **JSON**有问题？是不是哪里出问题了？答案就是：**domain 不能跟你的网页的 host 相同**。
>比如：微信打开你的网页，网页内有个“打开app”按钮，你点击“打开app”按钮，期望是跳到你的app。
毋庸置疑，“打开app”这个操作最终触发的链接是你上面配置好的 **domain** 的地址，那么这个时候，你这个网页的地址就不应该是带有 **domain** 的地址了。如果你网页是在 **domain** 下，而且你的“打开app”的地址也在 **domain** 下，那么就不会跳转到你的 **app**。我也不知道是为什么，理解的浅，但实际上这样就是不行。例子：
网页地址：http://host/xxx
“打开app”地址：https://domain/xxx?xxx=xxx
这里的host如果等于domain，就是我上面说的那种情况，直接在网页内打开了。如果host不等于domain，完美跳转至你的app。
当然，“打开app”这个操作实际上并没有那么简单。实际上还要多重判断。如果你并未安装该app，那么跳转到下载页面。如果你安装了该app，直接在触发这个domain链接的时候就跳到你的app了。

### iOS  会记录你的用户习惯
iOS 10下用通用链接跳转至你的app的时候，假设这里是微信网页里面点击了“打开app”跳转至你的应用，那么跳到你的应用的时候，你的应用顶部的statusbar左上角会出现“返回至微信”按钮，右上角会出现“用Safari打开xxx”。如下图：
![](http://upload-images.jianshu.io/upload_images/6067780-5daaab263bef97dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当你点击了右上角的这个按钮的时候，会跳转至Safari打开你的通用链接，iOS系统会记录你这个操作，认为你想在网页内打开你的domain下的通用链接，会导致你之后点击你的通用链接的时候，直接在Safari中打开了，而不是跳到你的app。
烦！
我测试了一下凤凰新闻和网易新闻，从微信跳转至他们的app之后，发现凤凰新闻的右上角的那个openInSafari可以点击并用Safari打开到指定页面，再次在微信内点击凤凰新闻的打开app按钮时，就直接在微信网页内打开了，而不是跳转到你的app。网易新闻就怪异了，点击微信内网易新闻的打开app按钮跳转到网易新闻app时，右上角的openInSafari是不可点的！好奇怪。
那么上面说的凤凰新闻一旦点击了右上角之后，后续在微信的凤凰新闻网页内打开app怎么再去跳转至凤凰新闻app呢？如图：
![微信内点击打开app](http://upload-images.jianshu.io/upload_images/6067780-4de512d9ad573fcf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Safari中打开](http://upload-images.jianshu.io/upload_images/6067780-211d769bd04de8b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![下拉点击打开](http://upload-images.jianshu.io/upload_images/6067780-a10591357e4550f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
依照凤凰新闻网页的提示一步步操作，到最后点击Safari中右上角的打开按钮时，那么iOS系统则再次认为用户点击凤凰新闻的通用链接时是想打开凤凰新闻的app而不是在网页内浏览。那么后续的分享至微信的凤凰新闻的网页内点击打开app的操作就会又是跳转到凤凰新闻app了。
我这里并没按照凤凰新闻的做法，而是研究了一下网易新闻到底是怎么禁掉右上角的openInSafari按钮的。就是我不给用户openInSafari的选择。
直接上代码：
- 在MainTBC监听UIApplicationDidBecomeActiveNotification
```
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(becomeActive) name:UIApplicationDidBecomeActiveNotification object:nil];
```
- 遍历statusbar子视图，找到UIStatusBarOpenInSafariItemView并禁用它
```
 - (void)becomeActive {
    NSLog(@"didBecomeActive");
    UIView *statusBar = [[UIApplication sharedApplication] valueForKey:@"statusBar"];
    NSLog(@"statusBar.subviews = %@", statusBar.subviews);
    //遍历子视图，找到右侧用Safari打开视图，禁用它。
    for (UIView *subView in statusBar.subviews) {
        NSLog(@"subView.subviews = %@", subView.subviews);
        for (UIView *aView in subView.subviews) {
            NSString *aViewClass = NSStringFromClass([aView class]);
            NSLog(@"aViewClass = %@", aViewClass);
            if ([aViewClass isEqualToString:@"UIStatusBarOpenInSafariItemView"]) {
                aView.userInteractionEnabled = NO;
                break;
            }
        }
    }
}
```
好吧，禁用掉了右上角进入Safari的按钮，不知道这样好不好，我一个朋友跟我说这样不好吧，暂且这样吧，其实可能还是有bug的，网易不知道是怎么做的，我做的只是在didbecomeactive里面的时候去遍历statusbar。

## 最后
还是我写在开头的那句话 **[官方文档 ](https://developer.apple.com/library/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1)**
## 最后的最后
如果有哪里写错了，欢迎指正，谢谢！


作者：winsonGali
链接：http://www.jianshu.com/p/eddc4365dcde
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

