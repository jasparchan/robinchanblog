title: Jenkins for iOS&Android（自动化构建）
date: 2016-02-29 09:56:40
tags: iOS
---
## 背景
目前：
	测试人员每次需要测试新版本，都需要开发人员打包，放到SVN，测试人员下载并安装。
	打包效率低，尤其是iOS，严重影响开发效率。
解决：
	使用持续集成（CI）系统jenkins，自动检测并拉取最新代码。
	自动打包ios的ipa和android的apk，自动上传到内测分发平台蒲公英或者FIR.
<!-- more -->
## 前置说明
1. 实现iOS项目自动打包，需要Mac OX环境
2. iOS自动打包脚本依赖于`xcodebuild`和`xcoderun`，所以要确保已安装Xcode；Android则要安装Gradle
3. iOS 项目使用 CocoaPods 进行依赖管理，故 Mac OSX 需要安装 CocoaPods
4. 需要确保 Jenkins 服务器所在的机器上拥有对应的证书和 Profile 文件，才能够顺利打包。

## 安装Jenkins
> 安装Jenkins

```
$ brew install jenkins
```

> 启动jenkins

```
$ jenkins
```

> 卸载jenkins

```
$ brew uninstall jenkins
```

> 若brew无效，请安装homebrew

```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

> jenkins需要java环境，http://www.oracle.com/technetwork/java/javase/downloads/index.html

当然还可以去官网下载安装包
> http://jenkins-ci.org

浏览器访问Jenkins
> 默认地址为：http://localhost:8080/, 可进入jenkins配置页面。


访问成功
![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/JenkinsInBrowser.png)

## 安装Jenkins相关插件
点击系统管理>管理插件>可选插件，可搜索以下插件安装
> git插件(GIT plugin) - 如果使用git拉取代码
> ssh插件(SSH Credentials Plugin)
> Post-Build Script Plug-in - 这个插件的功能主要是用于在build后执行相关脚本
> Xcode插件(Xcode integration) - ios专用
> Gradle插件(Gradle plugin) - android专用


## 新建Job
主页面，新建 -> 构建一个自由风格的软件项目即可。
对于类似的项目，可以选择 -> 复制已有的Item，要复制的任务名称里输入其他job的首字符会有智能提示。

## 配置SVN仓库
输入SVN地址后，会报错，根据提示进入另一个页面添加一个全局的SVN账号和密码。
![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/JenkinsSVN.png)
> 你还可以选择Git等方式拉取代码。

## 自动构建时机
这里就是告诉jenkins什么时候自动构建,这里我同时设置了每周一到周五在每天的8点到9点之间执行一次,当然也可以不设置。
![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/JenkinsPollSCM.png)

## 配置xcode - iOS专用
### 基本配置
> 这里Target请于Xcode项目中Target的名字对应,直接填${JOB_NAME}即可
> Clean before build设置为YES
> Configuration我选择了Release（在Release的时候Archive）
> .ipa filename pattern 随便起个.ipa的名字
> Output directory为.ipa的输出路径

![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/JenkinsXcode1.png)
> 如果你使用CocoaPods，而拉取的代码没有Pods目录，那你要在构建的最前面运行以下脚本。

```
pod install --verbose --no-repo-update
```

### 证书
> 这里是是使用用户的证书，jenkins会选择对应的证书进行打包${HOME}/Library/Keychains/login.keychain
> 也可以在jenkins系统设置里面进行全局设置

![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/JenkinsXcode2.png)
### Schema
> 需要Shared Schema文件.指定编译

![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/JenkinsXcode3.png)


## 配置gradle - Android专用
> 需要安装gradle插件，还有必备的Android SDK

![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/jenkins_gradle_config.png)
${WORKSPACE}表示当前job下的workspace目录，主要是存放代码。更多的环境变量请参考文末附录。
这样，就能自动在project下的app的build/outputs/apk下生成相应的apk.

> 注意：需要设置 GRADLE_HOME 环境变量并将 GRADLE_HOME/bin 加到 PATH 环境变量中。

## 上传到蒲公英或者FIR
### 操作步骤
> 1. Execute a set of scripts
> 2. Add build step
> 3. Execute 
> 4. 在Commad中输入

![](http://7sbydq.com1.z0.glb.clouddn.com/static/images/JenkinsBuildLater.png)
#### 蒲公英
具体到官网：https://www.pgyer.com/doc/view/upload_one_command
```
	curl -F "file=@{$filePath}" \
	-F "uKey={$uKey}" \
	-F "_api_key={$apiKey}" \
	https://www.pgyer.com/apiv1/app/upload
```

> 1. {$filePath}是应用安装包文件的路径
> 2. {$uKey}是开发者的用户 Key，在应用管理-API中查看
> 3. {$apiKey}是开发者的 API Key，在应用管理-API中查看

#### FIR
具体到官网：http://fir.im/support/articles/app_publish/publish
```
fir p ${WORKSPACE}/build/TestJenkins.ipa -T #API Token#
```

> 其中${WORKSPACE}/build/为4.5中.ipa的输出路径，#API Token#为fir.im的API Token。新版fir.im获取方式见下面。
请注意，在使用该命令前请先确认安装fir-cli，安装命令如下。

```
sudo gem install fir-cli --no-ri --no-rdoc
```
> 或使用插件fir的jenkins插件，具体从这篇文章看http://blog.fir.im/jenkins/
> 其实插件就是各种脚本的集合

## 参考资料
1. [Jenkins+GitHub+Xcode+fir搭了一个持续集成环境](http://www.jianshu.com/p/a17167274463)
2. [使用jenkins自动化构建android和ios应用](http://www.jayfeng.com/2015/10/22/%E4%BD%BF%E7%94%A8jenkins%E8%87%AA%E5%8A%A8%E5%8C%96%E6%9E%84%E5%BB%BAandroid%E5%92%8Cios%E5%BA%94%E7%94%A8/)