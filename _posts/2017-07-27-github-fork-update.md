---
layout: post
title: github上fork了别人的项目后，再同步更新别人的提交
categories: 工具
description: github fork update
tags: GitHub fork
cover: /assets/media/github-logo.png
---
* content
{:toc}
github上fork了别人的项目后，再同步更新别人的提交

## github网站上操作

### 1. 打开自己的仓库，进入code下面。
### 2. 点击new pull request创建。


![](/assets/media/15012096825773.png)


### 3.选择base fork

### 4.选择head fork

### 5.点击Create pull request，并填写创建信息。
![](/assets/media/15012096994606.png)
![](/assets/media/15012097059876.png)

### 6.点击Merge pull request 合并从源fork来的代码。
![](/assets/media/15012097289236.png)


### 7.完成。

## 用git命令操作

### 1.用git remote查看远程主机状态

```
git remote -v 
git remote add upstream git@github.com:xxx/xxx.git
git fetch upstream
git merge upstream/master
git push 
```
![](/assets/media/15012097709892.jpg)
![](/assets/media/15012097803958.jpg)
![](/assets/media/15012097890762.jpg)



