---
layout: post
title: 移动端，vue+clipboard.js，复制一段密令的文本到剪切板
subtitle: 类似淘宝支付宝口令
date: 2019-03-01
author:     "zhoupeng"
header-img: "img/post-bg-2015.jpg"
tags: 
    - vue
    - js经验篇
---

### 需求

开发过程中遇到的需求需要复制 请求过来的一段密令（类似支付宝淘宝口令）到剪切板。

### pc端的文本复制到剪切板

如果是在pc端的话，可以直接使用原生js进行编写，代码如下：

```
<textarea id="input">这是被复制的内容</textarea>
<button onclick="copyText()">copy</button>

<script type="text/javascript">
    function copyText() {
    	var text = document.getElementById("text").innerText;
    	var input = document.getElementById("input");
    	input.value = text; // 修改文本框的内容
    	input.select(); // 选中文本
    	document.execCommand("copy"); // 执行浏览器复制命令
    	alert("复制成功");
    }
</script>
```

但是这种方法在移动端会有一些问题，我是在微信公众号开发，发现在微信浏览器中会失效。

网上搜了下，`clipboard.js`是个相对比较靠谱的第三方。

### clipboard.js

[官方github](https://github.com/zenorocha/clipboard.js)

目前的star 25k ⭐️

[大佬的中文翻译](https://www.cnblogs.com/yunser/p/7628031.html)

### 概括一下

两种方式，一种适合复制固定的文本，另一种适合复制从后端获取的数据文本;

### 具体操作

#### 安装

通过npm安装

```
npm install clipboard --save
```

通过script+链接导入,可以去[BootCDN](https://www.bootcdn.cn/clipboard.js/)复制个自己想要的

```
<script src="https://cdn.bootcss.com/clipboard.js/2.0.4/clipboard.js"></script>
```

#### 使用

如果是vue文件，只需要在相关的组件中进行clipboard.js导入即刻。

```
import Clipboard from "clipboard";
```

编写需要进行复制的相关代码

如果是复制固定文本则如下：

```
<button
    class="copy-btn"
    @click="getAnswerNiceName"
    ref="copy"
    data-clipboard-text="这是固定的需要被复制到剪切板的内容"
>复制口令</button>
```

如果是复制从服务器获取的数据则如下：

```
<input
    id="copy-input"
    readonly  	 //如果在移动端，一定要再input中加上`readonly`属性，否则会拉起手机输入框
/>  			 //这个input可以进行相应的隐藏，例如绝对定位等	
<button
    class="copy-btn"
    @click="handleCopy"
    ref="copy"
    data-clipboard-action="copy"
    data-clipboard-target="#copy-input"
>复制口令</button>

....

methods: {
	handleCopy(){
    	axios.post('请求链接').then(res=>{
        	var input = document.getElementById("copy-input");
        	input.value = res.data  	//将获取的数据复制给input
            setTimeout(() => {
          		const copybtn = this.$refs.copy;
          		this.clipboard = new Clipboard(copybtn);
        	}, 100);  					//利用延时器防止内容没有及时更新等导致报错
        })
    }
}

```

延时器代码部分，也可以使用vue提供的 [nextTick](https://cn.vuejs.org/v2/api/#Vue-nextTick) 方法。 //感谢 Grewer 大佬的评论意见

使用该方法，会在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。代码如下：

```
this.$nextTick(() => {
    const copybtn = this.$refs.copy;
    this.clipboard = new Clipboard(copybtn);
});
```

也可以做一些复制成功与否的判断：

```
clipboard.on('success', function(e) {
    console.info('Action:', e.action);
    console.info('Text:', e.text);
    console.info('Trigger:', e.trigger);
    alert('复制成功，您复制的链接为'+e.text);
    e.clearSelection();
});

clipboard.on('error', function(e) {
    console.error('Action:', e.action);
    console.error('Trigger:', e.trigger);
    alert('复制失败')
});
```

这位大佬的demo代码也可以进行参考，亲测有用 -> [使用clipboard.js实现js复制黏贴的功能](https://blog.csdn.net/qq_33485463/article/details/82899752)

#### 可能存在的兼容性问题

网上说这个插件可能存在一些兼容性问题，我自己测试了多个手机，包括安卓和ios，暂时还未发现有兼容问题的存在，如果遇到了我再来及时更新

#### 最后

感谢各位大佬的参阅，如果有什么问题都可以在评论区提出。

如果觉得对您有帮助的话，也可点个赞，点个收藏，点关注不迷路。

转载请注明：[周鹏的博客](https://ttypzhoupeng.github.io/my-blog) » [点击阅读原文](https://ttypzhoupeng.github.io/my-blog/2019/03/01/vue_clipboard/)