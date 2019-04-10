---
layout: post
title: 个人新Mac 环境配置
# date: 2019-04-04 
author:     "zhoupeng"
# header-img: "img/post-bg-daily-excerpts.jpg"
tags: Mac
---


由于工作的需要以及自己电脑的更换，我们时常要重新配置我们的新电脑，包括开发环境以及一些娱乐环境。

为了自己不用每次重头来一遍，在这一次更换新mac电脑的时候，决定自己记录一下自己所需要的环境。也可以给大家做一些参考

# 娱乐
主要是日常休闲时光 所需。
* QQ
* 微信
* 腾讯视频
* 百度网盘
* 网易云音乐

这些都可以在app store中直接下载，账号都是通用的，数据都存在云上了，也就不需要同步啥了，直接登录账号就行，以前在app store下载过，直接登录appId即可，相对比较容易还原。

## [geekbench](http://soft.onlinedown.net/soft/586067.htm)
新买来的mac, 想要测试下自己电脑到底如何，那么怎么能少了跑分软件呢，对吧。


## screenfetch 
用代码来炫酷的展示系统信息

安装方式:
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew install screenfetch
```

# 学习以及工作
## Chrome
可通过app store直接下载

mac自带的safari也非常不错，但是我平常用的比较多的都是Chrome，所以很多的书签和习惯都在Chrome上了。

>Chrome自带的有同步的功能(会科学上网)

如果你懂得科学上网，那你肯定知道该怎么用这个功能，只要登录自己的账号无论走到哪都能同步自己平常几乎所有的数据，包括书签，平常记住的账号密码等等。

>Chrome书签的导入导出

当然不会科学上网页没关系，可以使用备用方法，到处书签，然后拷贝导出的书签文件，实际是个html网页文件，然后在新的电脑上导入chrome就行了，不过也仅限于书签的导入了。

## [iTerm2](https://www.iterm2.com/)

作为码农，肯定少不了终端替代工具iTerm2

初始化的iTem2并不符合大多数人的审美，所以网上有很多的主题可供下载安装。如果都没有自己喜欢的，那可以自己简单配置属于自己的主题。

步骤如下：iTerm2 - Preferences - Profiles

我的是这样的，

![iTerm2](/my-blog/img/posts/mac/iTerm2.png)

我的配置如下
### Colors
* Foreground: e7e3e3
* Background: ea4f62
* Bold: ffffff
* Links: 005bbb


* Cursor: fffffff
* Cursor text: 000000

### Text

font: 15pt Monaco

### Window
* Transparency: 10% （差不多）
* Blur: 5% (差不多)




## Xcode 命令行工具 Command Line Tools

### 简单介绍

简单来讲 `xcode命令行工具包` 是一个小型独立包，为mac终端用户提供了许多常用的工具，实用程序和编译器。例如常见的git，svn，gcc，

还有
make，clang，perl，size，strip，strings，libtool，cpp，what等等。安装这个`xcode命令行工具包`，就不用单独全装其他的了。


从MacOS High Sierra，Sierra，OS X El Capitan，Yosemite，Mavericks开始，可以单独下载这个`xcode命令行工具包`，无需先安装整个Xcode软件包，也无需开发人员帐户。

### 安装
```
xcode-select --install
```

### 详细可以参阅另一篇文章
[Mac OS X中安装命令行工具Command Line Tools](https://ttypzhoupeng.github.io/my-blog/2018/12/29/Command_Line_Tools/)

## iTerm2 zsh

参考文章:

[zsh官网](https://ohmyz.sh/)

[大佬参考文章](https://www.jianshu.com/p/9c3439cc3bdb)


## node环境 npm cnpm

参考大佬博客：

[mac 10.13.6 node.js和cnpm的安](https://www.jianshu.com/p/19decbb314a2)




#### 最后

感谢各位大佬的参阅，如果有什么问题都可以在评论区提出。

如果觉得对您有帮助的话，也可点个赞，点个收藏，点关注不迷路。

<!-- 转载请注明：[周鹏的博客](https://ttypzhoupeng.github.io/my-blog) » [点击阅读原文](https://ttypzhoupeng.github.io/my-blog/2019/04/04/js_basic/) -->
