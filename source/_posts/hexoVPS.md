---
title: 搭建Hexo博客（Github+Travis+VPS）
date: 2016-05-10 11:50:15
tags:
  - Github
  - Travis
  - VPS
---
## 前言
  博客是托管到Github和Coding，所以域名解析到相应的记录，但因对域名邮箱的需求，而再设置邮箱解析的话，就会造成CNAME记录冲突。这时候，想到我的VPS只是用来翻墙，有点浪费资源，遂将博客迁移到VPS。这时候域名就不会出现CNAME记录冲突，挺好的。
## 搭建环境和流程
1. 本机：Mac OX、Node.js、Hexo、Git、Travis
2. VPS服务器：Nginx、Git、Sudo
3. 流程：
	1. 本机提交hexo博客源码
	2. Travis收到代码变更通知，拉取最新代码，并`hexo g`生成博客静态页面，再`hexo d`部署到VPS(或Github或Coding)
	3. VPS则同步相应的Public文件夹:存放静态页面
	4. Web浏览器访问
<!-- more -->
## 安装Hexo
[Hexo官方文档](https://hexo.io/zh-cn/docs/)
```
// 本机安装hexo不加权限会报错，安装命令加上sudo
sudo npm install -g hexo
```
安装完毕，并初始化博客后，在你的github建立一个repository
## Travis CI 自动部署 hexo
### 公钥密钥
打开终端，本地生成id_rsa(私钥)，id_rsa.pub（公钥）
```
$ git config --global user.email "#邮箱" 
$ git config --global user.name "#用户名" 
$ ssh-keygen -t rsa -C "youremail@example.com" #一路回车就好
```
公钥（id_rsa.pub）和私钥（id_rsa）默认生成在:~/.ssh/ 目录下。
复制公钥到github、coding对应官网的SSH keys
后面也用来免密码登陆vps
### 建立hexo代码库
上面我们已经建立了hexo博客的代码库，现在需要有一个 Travis CI 的账号，直接进入 [Travis CI](https://travis-ci.org/) 官网，用自己的 Github 账号授权登录即可。然后可以看到当前账号的所有代码仓库，接下来将博客项目的状态设置为启用。如下图
![image](http://7sbydq.com1.z0.glb.clouddn.com/opentravis.png)
### 配置Travis CI
私钥（id_rsa) 需要随博客代码库提交到github，所以必须加密为好。
刚才公钥文件配置在github，然后配置私钥文件
   
   ```
   $ cd #根目录
   ```
   
   在 hexo 项目下面建立一个 .travis 的文件夹来放置需要配置的文件。
   ```
   $ mkdir .travis
   ```
   
   建立travis的配置文件
   ```
   $ touch .travis.yml
   ```
   
   首先要安装 travis 命令行工具(温馨提示：使用[淘宝源](https://ruby.taobao.org/)速度更快哦)。
   ```
   $ gem install travis
   ```
   
   用命令行工具登录
   ```
   $ travis login --auto
   ```
   然后将刚刚生成的 id_rsa 复制到根目录，用命令行工具进行加密：
   ```
   $ travis encrypt-file id_rsa --add 
   ```
   这时候会生成加密后的密钥文件id_rsa.enc，删掉原来的id_rsa，把id_rsa.enc移到.travis文件夹为了让 git 默认连接 SSH 还要创建一个 ssh_config 文件。
   ```
   $ touch .travis/ssh_config
   ```
   在 .travis 文件夹下创建一个 ssh_config 文件，输入以下内容：
    ```
    Host github.com
    User git
    StrictHostKeyChecking no
    IdentityFile ~/.ssh/id_rsa
    IdentitiesOnly yes
    #
    #如果要部署到多个地方，可以追加
    #
    Host #IP  eg.VPS、Coding
    User git
    StrictHostKeyChecking no
    IdentityFile ~/.ssh/id_rsa
    IdentitiesOnly yes
    ```
    
   最后就要配置 .travis.yml
```
# 配置语言
language: node_js
sudo: true
# 项目所在分支
branches:
  only:
  - master  
# 缓存node_modules，节省部署时间
cache:
  directories:
  - node_modules
#部署之前配置
before_install:
#把私钥解密，这里需要注意私钥的路径
- openssl aes-256-cbc -K $encrypted_372fe9aee9a2_key -iv $encrypted_372fe9aee9a2_iv
  -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
 # 改变文件权限
- chmod 600 ~/.ssh/id_rsa
# 配置 ssh
- eval $(ssh-agent)
- ssh-add ~/.ssh/id_rsa
- cp .travis/ssh_config ~/.ssh/config
# 配置 git
- git config --global user.name 'your name'
- git config --global user.email your email
# 安装依赖
install:
- npm install hexo-cli -g
- npm install
# 部署
script:
- hexo clean
- hexo g
- hexo d
```
   
这里有一份我的 [travis](https://github.com/jasparchan/robinchanblog/blob/master/.travis.yml) 配置文件，下面是我的目录
![image](http://7sbydq.com1.z0.glb.clouddn.com/hexoDirectory.png)
   
   到这里，当我们commit hexo代码后，travis会帮我们执行`hexo clean``hexo g``hexo d`等命令。但`hexo d`，也就是部署命令，还需要做下一步
## 部署到VPS与Git Hooks更新
### 创建站点管理者
连接ssh，（本人vps的系统为Centos 6 x86_64 minimal）
```
$ ssh root@vps id #输入密码
```

创建用户组和用户
```
$ groupadd sitesManagers #创建用户组
$ useradd #your name -m -g sitesManagers #创建用户
```
设置用户密码
```
$ passwd #your name
```

设置用户目录权限
```
$ chmod 755 /home/#your name
```

给用户添加sudo权限,(若没安装sudo，可`yum install sudo`,安装后才会有`/etc/sudoers`目录）
```
$ cd /etc/sudoers
# 找到以下指令添加
## Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
#your name   ALL=(ALL)     ALL #新增这条指令
```

设置VPS上ssh的端口号，将/etc/ssh/sshd_config文件中的Port为22设置如下：
```
$ cd /etc/ssh/sshd_config
# 更改
# Port 22
```

### 使用刚创建的管理者免密码登陆VPS
拷贝公钥到远程服务器上,username换成你的用户名，和服务器地址,会提示输入密码
```
$ scp ~/.ssh/id_rsa.pub username@hostname.com:~/.ssh/
```

登陆远程服务操作
```
$ ssh username@hostname.com
```

输入密码后登录，也许是你最后一次登录服务器需要密码，接着需要在服务器上添加公钥。
```
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys 
```

Done!`logout`后，再`ssh username@hostname.com`就无需输入密码啦

[另外一种方法](http://blog.csdn.net/u013066244/article/details/52796341)

### 配置nginx和git
登陆vps（root 用户）
```
$ ssh your vps id #root用户
```

安装git
```
$ yum install git
```

安装nginx,在/etc/yum.repos.d目录下创建一个源配置文件nginx.repo,写入如下代码
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

```
$ yum install nginx -y
$ yum -v #查看nginx版本号
```

登陆vps（站点管理者用户）
```
$ ssh username@hostname.com #站点管理者用户
```
创建git仓库，注意目录。
```
$ cd /home/username
$ mkdir hexo.git
$ cd hexo.git
$ git init --bare
```

配置git hooks
```
$ cd /home/username/git/hexo.git/hooks
$ vi post-receive  //我安装时该目录内有~.sample文件，可以改名下或者直接新建一个。
#
输入以下内容
#!/bin/bash
GIT_REPO=/home/username/hexo.git  //上面自己创建的文件夹
TMP_GIT_CLONE=/tmp/hexo
PUBLIC_WWW=/var/www/hexo  //网站文件目录，需要自己创建
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
#
再执行
$ chmod +x post-receive
#
接着创建目录
/var/www/hexo
并赋予权限
$ sudo chmod 777 -R /var/www/hexo
```

配置nginx
```
$ vi /etc/nginx/conf.d/default.conf
```
我修改的一部分
```
server {
    listen       80;
    server_name  www.robinchan.cn robinchan.cn blog.robinchan.cn *.robinchan.cn;
    location / {
        root   /var/www/robinchan.cn/public;
        index  index.html index.htm;
    }
    error_page  404              /404.html;
}
server {
    listen       80  default_server;
    server_name  _;
    return       404;
}
```

重启nginx
```
$ service nginx restart
```

### Hexo deploy配置
博客目录下的_config.yml文件
```
deploy:
  type: git
  message: update done
  repo:
    github: git@github.com:jasparchan/jasparchan.github.io.git,master
    gitcafe: git@git.coding.net:jasparchan/jasparchan.git,master
    vps: RobinChan@robinchan.cn:hexo.git,master
```

如果vps默认ssh端口没有改为22的话
```
ssh://RobinChan@robinchan.cn:your port/~/hexo.git
```

over

## 参考文章
感谢能让我少走弯路的这些文章
1. [在bandwagonhost搬瓦工上搭建Hexo博客并使用git hooks自动部署](http://xyz0z0.xyz/2016/02/10/%E5%9C%A8bandwagonhost%E6%90%AC%E7%93%A6%E5%B7%A5%E4%B8%8A%E6%90%AD%E5%BB%BAHexo%E5%8D%9A%E5%AE%A2%E5%B9%B6%E4%BD%BF%E7%94%A8git-hooks%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2/)
2. [VPS(CentOS)搭建Hexo博客与Git Hooks更新（小白篇）](http://www.hansoncoder.com/2016/03/02/VPS-building-Hexo/)
3. [ssh-keygen配合ssh_config免密码登录VPS](http://blog.csdn.net/chen_chun_guang/article/details/42297011)
4. [用 Travis CI 自動部署網站到 GitHub](https://zespia.tw/blog/2015/01/21/continuous-deployment-to-github-with-travis/)


