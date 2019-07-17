---
layout: post
title: html dom 转化成图片踩坑记（canvas toDataURL）
date: 2018-10-09
author:     "zhoupeng"
header-img: "img/post-bg-js-module.jpg"
tags: 
    - HTML5  
    - js经验篇
---

## 需求

在开发过程中遇到这么一个需求，h5页面需要将一个html dom转化成图片，便于用户保存。

## 面向百度搜索第三方得 html2canvas 和 dom-to-image

两者在写这篇笔记之前在github上的星星数分别是

dom-to-image  4k ⭐️

html2canvas 13.7k ⭐️

两者都有尝试过，都有意想不到的bug，包括

1. 部分手机有某些背景图片无法展示，为空白
2. iphone8 plus ios 11中根本不调用这个转换方法，从而得不到想要的图片。
等等

## 自己动手

### 思路

利用canvas的toDataURL来拿到canvas转化的base64码，来替换img的url, 也可以把图片上传到公司的服务器上，得到图片的地址来进行下载，或作为参数来传递

那么canvas的绘制主要就是文本和图片的绘制,文本绘制相对简单，图片绘制有一些注意点。

### canvas 初始化

>注意：由于最后生成的图片可能会模糊，可以尽量画大一点画布，可以按照设计图来

```
<canvas id="canvas" width="750" height="1164">
  你的浏览器居然不支持Canvas？！赶快换一个吧！！
</canvas>
```

```
let c = document.getElementById("canvas");
let ctx = c.getContext("2d");
```

### 文本绘制

#### 官方文档如图1

![文本绘制](/my-blog/img/posts/canvas_toDataURL/1.png)

#### 详细文档请参考 [canvas手册](http://www.w3school.com.cn/tags/html_ref_canvas.asp)

#### 代码示例

```
ctx.fillStyle = "#fff";
ctx.font = "32px PingFangSC-Regular";
ctx.textAlign = "left";
ctx.fillText("这是文本", 280, 755);
```

### 图片绘制

#### 官方文档如图2

![文本绘制](/my-blog/img/posts/canvas_toDataURL/2.png)

#### 详细文档请参考1 [canvas手册](http://www.w3school.com.cn/tags/html_ref_canvas.asp)

#### 注意事项

1. 图片需要进行跨域处理，否则后期无法生成图片，也就是在img标签中增加crossOrigin属性，值为anonymous

```
const instBanner = document.getElementById("instBanner");
instBanner.crossOrigin = "anonymous";
```

2. 需要等到图片加载完成再画到画布上，否则有可能没画上去

```
const posterBg = new Image();
posterBg.src = mainBg;
posterBg.onload = () => {
  ctx.drawImage(posterBg, 0, 0, 750, 1164);
}
```

#### 完整代码示例

```
const posterBg = new Image();
posterBg.src = 'https:....'; //这里是图片url
posterBg.crossOrigin = "anonymous";
posterBg.onload = () => {
  ctx.drawImage(posterBg, 0, 0, 750, 1164);
}
```

### 生成图片

#### 替换img src 

```
let dataURL = c.toDataURL("image/png");
let canvasImg = document.getElementById("canvasImg");
canvas.src = dataURL;
```

#### 上传服务器，得到img url(可作为参数，保存图片)

```
let dataURL = c.toDataURL("image/png");
function getImgUrl(dataURL){
  //一些上传服务器的代码
  return imgUrl
}
let imgUrl = getImgUrl();
let canvasImg = document.getElementById("canvasImg");
canvas.src = imgUrl;
```

### 最后奉上一些，常用的canvas处理方法

#### 圆形图片的绘制

```
ctx.save();

ctx.beginPath(); //开始绘制
//先画个圆   前两个参数确定了圆心 （x,y） 坐标  第三个参数是圆的半径  四参数是绘图方向  默认是false，即顺时针
ctx.arc(60, 60, 30, 0 * Math.PI, 2 * Math.PI);

ctx.clip();//画好了圆 剪切  原始画布中剪切任意形状和尺寸。一旦剪切了某个区域，则所有之后的绘图都会被限制在被剪切的区域内 这也是我们要save上下文的原因

ctx.drawImage('https:....', 30, 30, 60, 60);

contex.restore(); //恢复之前保存的绘图上下文 恢复之前保存的绘图上下午即状态 还可以继续绘制
```

#### 圆角矩形绘制

```

/**该方法用来绘制圆角矩形 
*@param cxt:canvas的上下文环境 
*@param x:左上角x轴坐标 
*@param y:左上角y轴坐标 
*@param width:矩形的宽度 
*@param height:矩形的高度 
*@param radius:圆的半径 
*@param lineWidth:线条粗细 
*@param strokeColor:线条颜色 
**/  
function strokeRoundRect(cxt,x,y,width,height,radius,/*optional*/lineWidth,/*optional*/strokeColor){  
    //圆的直径必然要小于矩形的宽高          
    if(2*radius>width || 2*radius>height){return false;}  
      
    cxt.save();  
    cxt.translate(x,y);  
    //绘制圆角矩形的各个边  
    drawRoundRectPath(cxt,width,height,radius);  
    cxt.lineWidth = lineWidth||2;//若是给定了值就用给定的值否则给予默认值2  
    cxt.strokeStyle=strokeColor||"#000";  
    cxt.stroke();  
    cxt.restore();  
}  

/**该方法用来绘制一个有填充色的圆角矩形 
*@param cxt:canvas的上下文环境 
*@param x:左上角x轴坐标 
*@param y:左上角y轴坐标 
*@param width:矩形的宽度 
*@param height:矩形的高度 
*@param radius:圆的半径 
*@param fillColor:填充颜色 
**/
function fillRoundRect(cxt,x,y,width,height,radius,/*optional*/fillColor){  
    //圆的直径必然要小于矩形的宽高          
    if(2*radius>width || 2*radius>height){return false;}  
      
    cxt.save();  
    cxt.translate(x,y);  
    //绘制圆角矩形的各个边  
    drawRoundRectPath(cxt,width,height,radius);  
    cxt.fillStyle=fillColor||"#000";//若是给定了值就用给定的值否则给予默认值  
    cxt.fill();  
    cxt.restore();  
}  

function drawRoundRectPath(cxt,width,height,radius){  
    cxt.beginPath(0);  
    //从右下角顺时针绘制，弧度从0到1/2PI  
    cxt.arc(width-radius,height-radius,radius,0,Math.PI/2);  
  
    //矩形下边线  
    cxt.lineTo(radius,height);  
  
    //左下角圆弧，弧度从1/2PI到PI  
    cxt.arc(radius,height-radius,radius,Math.PI/2,Math.PI);  
  
    //矩形左边线  
    cxt.lineTo(0,radius);  
  
    //左上角圆弧，弧度从PI到3/2PI  
    cxt.arc(radius,radius,radius,Math.PI,Math.PI*3/2);  
  
    //上边线  
    cxt.lineTo(width-radius,0);  
  
    //右上角圆弧  
    cxt.arc(width-radius,radius,radius,Math.PI*3/2,Math.PI*2);  
  
    //右边线  
    cxt.lineTo(width,height-radius);  
    cxt.closePath();  
}  
```

