---
layout: post
title: Mac OS X中安装命令行工具Command Line Tools
subtitle: 无Xcode安装
date: 2018-12-29
author:     "zhoupeng"
header-img: "img/post-bg-2015.jpg"
tags: Mac环境配置
---

## 简介
喜欢通过终端访问更传统的Unix工具包的Mac用户（大部分是程序员），都会选择安装Xcode IDE的可选命令行工具子部分，也就是`Command Line Tools`。

从MacOS High Sierra，Sierra，OS X El Capitan，Yosemite，Mavericks开始，无需先安装整个Xcode软件包，也无需登录开发人员帐户，就可以单独安装`Command Line Tools`。

>- 所以本篇文章适用于MacOS 10.13 High Sierra，10.12 Sierra，OS X 10.11 El Capitan，OS X 10.10 Yosemite和Mac OS X 10.9以及更新版本。
>- 运行OS X早期版本的Mac用户可以访问这篇文章[Xcode Command Line Tools](http://railsapps.github.io/xcode-command-line-tools.html)

简单来讲 `Command Line Tools` 就是一个小型独立包，为mac终端用户提供了许多常用的工具，实用程序和编译器。包括svn，git，make，GCC，clang，perl，size，strip，strings，libtool，cpp，what以及其他很多能够在Linux默认安装中找到的有用的命令。

后面会列出这个工具包中完整的命令列表，也可以自己安装完后亲自查看。

## 安装

### 1.启动terminal或者iterm终端

### 2.输入以下命令字符串

```
 xcode-select --install
```

会出现以下弹窗，点击安装即可

![插图](/my-blog/img/posts/command/1.png)

### 3.接着会有一个同意服务条款弹出，点击同意即可

![插图](/my-blog/img/posts/command/2.jpeg)

### 4.等待下载

![插图](/my-blog/img/posts/command/3.jpeg)

### 5.完成安装后会提示安装完成，点击安装完成即可。

![插图](/my-blog/img/posts/command/4.jpeg)

### 6.然后可以通过尝试使用刚安装的命令之一来确认是否安装成功，例如

```
gcc -v
git version
...
```

![插图](/my-blog/img/posts/command/5.jpeg)

## 这个包实际安装在哪

如果你对这个包中到底有什么东西感兴趣，可以自己查看这个包中的内容。它安装在mac的根目录中
/Library/Developer/CommandLineTools/
>注意： 在根/目录下，而不是用户目录~/..

61个可用的新命令，都在/ Library / Developer / CommandLineTools / usr / bin /中。

下面按字母顺序列出这些命令：

```
ar
as
asa
bison
BuildStrings
c++
c89
c99
cc
clang
clang++
cmpdylib
codesign_allocate
CpMac
cpp
ctags
ctf_insert
DeRez
dsymutil
dwarfdump
dyldinfo
flex
flex++
g++
gatherheaderdoc
gcc
gcov
GetFileInfo
git
git-cvsserver
git-receive-pack
git-shell
git-upload-archive
git-upload-pack
gm4
gnumake
gperf
hdxml2manxml
headerdoc2html
indent
install_name_tool
ld
lex
libtool
lipo
lldb
lorder
m4
make
MergePef
mig
mkdep
MvMac
nasm
ndisasm
nm
nmedit
otool
pagestuff
projectInfo
ranlib
rebase
redo_prebinding
ResMerger
resolveLinks
Rez
RezDet
RezWack
rpcgen
segedit
SetFile
size
SplitForks
strings
strip
svn
svnadmin
svndumpfilter
svnlook
svnrdump
svnserve
svnsync
svnversion
unifdef
unifdefall
UnRezWack
unwinddump
what
xml2man
yacc
```

参考文章：[How to Install Command Line Tools in Mac OS X (Without Xcode)][1]



  [1]: http://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/