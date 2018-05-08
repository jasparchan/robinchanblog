---
title: Mac上配置Android开发环境
date: 2018-04-08 15:11:14
tags:
---

如果没有安装Homebrew
```
    ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"
```
使用`Homebrew`和`Homebrew Cask`安装所有的安卓开发工具
```	
    brew install ant
    brew install maven
    brew install gradle
    brew cask install android-sdk
    brew cask install android-ndk

```

更新环境变量，打开`.bash_profile`，最后添加
```
    export ANT_HOME=/usr/local/opt/ant
    export MAVEN_HOME=/usr/local/opt/maven
    export GRADLE_HOME=/usr/local/opt/gradle
    export ANDROID_HOME=/usr/local/share/android-sdk
    export ANDROID_NDK_HOME=/usr/local/share/android-ndk
    export PATH=$ANT_HOME/bin:$PATH
    export PATH=$MAVEN_HOME/bin:$PATH
    export PATH=$GRADLE_HOME/bin:$PATH
    export PATH=$ANDROID_HOME/tools:$PATH
    export PATH=$ANDROID_HOME/platform-tools:$PATH
    export PATH=$ANDROID_HOME/build-tools/27.0.3:$PATH

```

我这里使用的是Android Studio,你也可以用Eclipse、IntelliJ等等你喜欢的IDE.

```
    brew cask install android-studio
```

