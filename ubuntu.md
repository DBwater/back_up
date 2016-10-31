---
title: ubutn上常用软件的配置
date: 2016-10-31 23:19:44
tags: [ubuntu]
---
记录常用的ubuntu软件配置
<!--more-->
系统被窝玩坏了(滑稽.jpg)然后又重装系统了，真是惨啊，感觉这几天真是特别倒霉，系统崩溃，上课跑错教学楼，记错实验时间什么的，刚买的口琴吹了两天就坏了(ps:谁说德国制造的都是精品，你出来窝保证不打死你( ´∀`)σ)Д`))，然后寄回去了，是我吹两只老虎吹的太大力？这个锅窝不背啊，真是TAT，感觉整个人都不好了，迷之状态真可怕，惟一的好消息就是我的滑稽到了，一只大滑稽加上一只小滑稽，感觉整个人都滑稽起来了QwQ，心情又稍微好了一点，结果为了配置vim和重装系统又浪费了两天的时间，说好复习的也没复习，为了以后重装系统的时候节约时间，我在这记录下一些ubuntu常用的软件安装的方法。
# 安装基本软件
## 安装git
> sudo add-apt-repository ppa:git-core/ppa
sudo apt-get update
sudo apt-get install git-core

## 安装zsh和oh-my-zsh
> sudo apt-get install zsh
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
chsh -s /usr/local/bin/zsh

然后重启命令行就行

## 安装pip
> sudo apt-get install python-pip

因为pip的源被墙了，所有窝们更改pip的源
> cd ~
mkdir .pip
gedit pip.conf

然后添加

> [global]
trusted-host = pypi.douban.com
index-url = http://pypi.douban.com/simple

## 安装翻墙工具
> sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get install update
sudo apt-get install shadowsocks-qt5

## 安装chrome
给一个不用翻墙的网站 [chrome](http://www.chrome64bit.com/index.php/google-chrome-64-bit-for-linux)
下载之后直接安装就行了

## 安装node
由于node更新的比较快可以直接去官网下载，百度node.js下载即可

# 配置blog
## 下载hexo
 更改npm的源用cnpm代替
>npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install hexo-cli -g
cnpm install hexo 

## 配置myblog文件夹
>mkdir myblog
cd myblog
hexo init
cnpm install

然后把myblog中的_config.yml最下方添加
```
deploy: 
  type: git
  repository: http://github.com/kingofprank/kingofprank.github.io.git
  branch: master
```
最后在命令行输入：
>npm install hexo-deployer-git --save

博客就配置完成了。
还有什么软件，想到我会再补充
