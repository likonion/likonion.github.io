---
layout: post
title: Mac上使用Item2+lrzsz+autossh管理Linux服务器
categories: 工具
description: some word here
tags: Item2 autossh
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/img_no.gif.jpeg
---
* content
{:toc}
Mac下没有XShell这样好用的工具，所以需要经常性操作Linux服务器的我需要有一款趁手的工具。

## 需求背景

Mac 下没有 XShell 这样好用的工具，所以需要经常性操作Linux服务器的我需要有一款趁手的工具。日常操作的需求基本是这几个

* 在iTerm2环境下使用（Mac上这是最好用的终端吧）
* 支持使用rz和sz命令上传下载（为啥不是ftp？因为跳板机环境下不能使用）
* 可以管理用户名和密码（密码太复杂记不住）
* 支持使用pem格式的密钥文件（登录跳板机都是用密码+密钥的）

谷歌了很多方案，主流的是采用expect进行响应式操作。这个方法挺好的，非常灵活并且可以在跳板机环境下多次响应后直接登录。不过我在使用的过程中发现不能配合sz和rz使用，不知道是不是我的RPWT，反正我使用expect登录服务器之后，再使用sz/rz就进入假死状态。所以我最后选了另外一个叫autossh的工具，这个工具可以实现Session管理（用户，密码，密钥），但是不能响应式操作。
优点

* 可以免输密码登录服务器
* 配合rz/sz毫无违和感

缺点

* 如果是跳板机这种需要继续选择机器和二次输入密码的场景，就只能登录到跳板机界面后手工操作

## 在iTerm下配置rz/sz

Mac上iTerm原生不支持rz/sz命令，也就是不支持Zmodem来进行文件传输，不过只要通过简单的配置就可以实现。网上的教程一大把，这里就简单的记录一下过程。

### 安装lrzsz

1. 首先在客服端Homebrew(这里不写这个过程)，然后通过它先给Mac安装lrzsz。在终端下输入brew install lrzsz，静等一会即可安装完毕。
```sh
brew install lrzsz
```
2. 登录远程机器，该机器上需要有lrzsz（sudo yum install -y lrzsz)。

### 下载iTerm2辅助文件

iTerm不能直接使用lrzsz，不过网上有大神提供了两个辅助脚本。我们只需要把文件下载到 /usr/local/bin/目录下并赋予可执行权限即可。

```sh
cd /usr/local/bin
wget https://raw.githubusercontent.com/mmastrac/iterm2-zmodem/master/iterm2-send-zmodem.sh
wget https://raw.githubusercontent.com/mmastrac/iterm2-zmodem/master/iterm2-recv-zmodem.sh

chmod +x iterm2-recv-zmodem.sh iterm2-send-zmodem.sh
```

这两个脚本实际是使用AppleScript来弹出文件选择窗口，然后把选中的文件名称传递给rzsz命令。我们打开其中一个看下代码。如果这一部分看不懂没关系，直接跳过即可，对后续的配置使用没有任何不良影响 :-D

### 配置iTerm2触发器

这一步最关键，是在iTerm里面配置触发器，当监控到特定字符串的时候执行刚才下载的两个文件。为了使用方便，我专门建立了一个Profile配置，名字是Remote，并且配合后面的autossh使用。

打开iTerm2 -> Preferences -> Profiles 选择 Advanced 设置 Triggers ，点击 Edit

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/1637d8adabcfd4bc.png)

在弹出窗口中进行如下配置，最后的Instant一定要勾选上。

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/1637d8adb3ad407e.png)

配置的具体内容在这里

```
Regular expression: rz waiting to receive.\*\*B0100
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-send-zmodem.sh

Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
```

重新启动iTerm之后，rz/sz就应该可以正常使用了。

## autossh

如前文所说，当使用expect方案的时候rz/sz不能弹出文件选择窗口。我猜可能是expect启动的session上下文中不能正常执行AppleScript造成的，因为对这两个玩意儿不是很熟，所以也就没有办法深入研究和折腾。各种谷歌后，发现这个好东西 https://github.com/islenbo/autossh ，优秀的产品总是很简单的，就一个可执行文件，加上一个json配置文件。

让我们先来看看帮助信息

```
auto_ssh $ ./autossh -h
go写的一个ssh远程客户端。可一键登录远程服务器，主要用来弥补Mac/Linux Terminal ssh无法保存密码的不足。
基本用法：
  直接输入autossh不带任何参数，列出所有服务器，输入对应编号登录。
参数：
  -v, --version 	 显示 autossh 的版本信息。
  -h, --help    	 显示帮助信息。
操作：
  list          	 显示所有server。
  add <name>    	 添加一个 server。如：autossh add vagrant。
  edit <name>   	 编辑一个 server。如：autossh edit vagrant。
  remove <name> 	 删除一个 server。如：autossh remove vagrant。
```
### 服务器信息配置

配置文件servers.json也很容易编辑，看看官方提供的例子

```
[
  {
    "name": "vagrant", // 显示名称
    "ip": "192.168.33.10", // 服务器IP或域名
    "port": 22, // 端口
    "user": "root", // 用户名
    "password": "vagrant", // 密码
    "method": "password" // 认证方式，目前支持password和pem
  },
  {
    "name": "ssh-pem",
    "ip": "192.168.33.11",
    "port": 22,
    "user": "root",
    "password": "your pem file password or empty", // pem密钥密码，若无密码则留空
    "method": "pem", // pem密钥认证
    "key": "your pem file path" // pem密钥文件绝对路径
  }
  // ...可配置多个
]
```
### 添加环境变量

```sh
Mac系统下的环境变量
a. /etc/profile 
b. /etc/paths 
c. ~/.bash_profile 
d. ~/.bash_login 
e. ~/.profile 
f. ~/.bashrc 
```
> 其中a和b是系统级别的，系统启动就会加载，其余是用户接别的。c,d,e按照从前往后的顺序读取，如果c文件存在，则后面的几个文件就会被忽略不读了，以此类推。~/.bashrc没有上述规则，它是bash shell打开的时候载入的。这里建议在c中添加环境变量，以下也是以在c中添加环境变量来演示的:

1. 输入vim ./.bash_profile 确定enter，然后就打开了bash_profile文件
2. 进入编辑并输入以下内容
```sh
export autossh=/Users/vincentzhang/autossh
export PATH=$PATH:$autossh
```
3. 使用source ./.bash_profile使刚刚修改的文件生效。


### 使用方法

保存servers.json，执行autossh，即可看到相应服务器信息，输入对应序号，即可自动登录到服务器。下面的动图是借用官方的，原谅一下自己的懒惰 :-D

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/1637d8adb28085d6.gif)

## 用iTerm自动调用

前文说过，我在iTerm里面建立了一个叫Remote的Profile，目的就是为了在使用CMD+O打开一个新窗口的时候自动执行autossh，那么我们还需要在iTerm里面添加一点配置，打开窗口时自动运行一段shell。如下图

![](https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/1637d8adb29f77a4.png)
