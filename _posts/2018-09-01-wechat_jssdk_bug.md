---
layout: post
title: 微信网页开发调用微信jssdk接口遇到的坑以及最终解决方法 
subtitle: 持续更新ing..
date: 2018-09-01
author:     "zhoupeng"
header-img: "img/post-bg-js-module.jpg"
tags: 微信网页开发  
---

### 1.微信网页开发调用jssdk时报permission denied
#### 大致是两个原因

（1）首先注册时未将你所调用的接口名字添加至jsApiList


（2）第二个就是你的这个公众号没有权限使用这个api，例如在开发环境中的微信页面就无法调取这个api,需要发布后，到对应的有权限的公众号中再打开对应的界面，调用api才成功

### 2.微信jssdk 使用微信内置地图查看位置【openLocation】接口 最终显示不精确

项目开发中，web端调用的是百度地图api，而微信项目中调用的是腾讯自己的地图，两者所使用的坐标系有所不同，百度用的是自己的百度坐标系，而高德地图和腾讯地图使用的是火星坐标系，两者之间需要做一下转换

=>火星坐标 转换到 百度地图坐标

```
function huoxingToBaidu(hxLongitude, hxLatitude){  
                var X_PI = Math.PI * 3000.0 / 180.0;  
                var x = hxLongitude, y = hxLatitude;  
                var z = Math.sqrt(x * x + y * y) + 0.00002 * Math.sin(y * X_PI);  
                var theta = Math.atan2(y, x) + 0.000003 * Math.cos(x * X_PI);  
                var bdLongitude = z * Math.cos(theta) + 0.0065;  
                var bdLatitude = z * Math.sin(theta) + 0.006;  
                return {  
                    bdLongitude: bdLongitude,  
                    bdLatitude: bdLatitude  
                };  
            }  
```

=>百度地图坐标 转换到 火星坐标

```
function baiduToHuoxing(bdLongitude,bdLatitude) {  
                var X_PI = Math.Pi * 3000.0 / 180.0;  
                var x = bdLongitude - 0.0065;  
                var y = bdLatitude - 0.006;  
                var z = Math.sqrt(x * x + y * y) - 0.00002 * Math.sin(y * X_PI);  
                var theta = Math.atan2(y, x) - 0.000003 * Math.cos(x * X_PI);  
                var hxLongitude = z * Math.cos(theta);  
                var hxLatitude = z * Math.sin(theta);  
                return {  
                    hxLongitude: hxLongitude,  
                    hxLatitude: hxLatitude  
                }  
            }  
```

### 3.微信JSSDK 预览图片【previewImage】接口的坑, 安卓手机上，图片预览需要点击两次才能退出预览

在微信网页开发过程中，测试提出的一个bug，在微信中预览图片是，点击图片，打开图片正常，当再次点击时，原本应该直接退出图片预览，但是却需要点击两次才能退出预览。


#### 分析原因

应该是 previewImage 这个接口调用了两次，进行了图片的叠加。所以需要两次才能退出

#### 最终得出的结果

代码中实际上只调用了一次。

1. 在Android系统的手机中，其实微信点击网页的图片会自动调用这个接口一次。然后代码中又对对这个接口调用了一次，这就造成了以上的结果。

2. 但是在ios系统的手机中，微信并不会调用这个接口。

#### 解决方案

在方法的最前面进行手机系统的判断，当是Android系统直接return。
然后在在下面进行微信的previewImage接口的调用

#### js判断是Android还是ios

```
var u = navigator.userAgent;

isAndroid = u.indexOf('Android') > -1 || u.indexOf('Adr') > -1, //android终端

isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
```

--- 
2018.8.1更新
这个bug好像微信自己修复了，现在无论Android还是ios，如果不调用这个方法，微信自己不会去调用了。

#### 最后
感谢各位大佬的参阅，如果有什么问题都可以在评论区提出。

如果觉得对您有帮助的话，也可点个赞，点个收藏，点关注不迷路。

转载请注明：[周鹏的博客](https://ttypzhoupeng.github.io/) » [点击阅读原文](https://ttypzhoupeng.github.io/my-blog/2018/09/01/wechat_jssdk_bug/)

