---
layout: post
title: js基础篇-2.解构赋值
date: 2019-04-08 
author:     "zhoupeng"
header-img: "img/post-bg-js-version.jpg"
tags: js基础篇
---

# js基础查漏补缺

<!-- TOC -->

- [js基础查漏补缺](#js基础查漏补缺)
  - [1.解构赋值](#1解构赋值)
    - [1.1 使用解构赋值，快速获取对象的指定属性：](#11-使用解构赋值快速获取对象的指定属性)
    - [1.2 对嵌套对象属性赋值](#12-对嵌套对象属性赋值)
    - [1.3 利用新变量替代属性名](#13-利用新变量替代属性名)
    - [1.4 使用默认值](#14-使用默认值)
    - [1.5 有时候正确写法报错](#15-有时候正确写法报错)
    - [1.6 使用场景，简化代码](#16-使用场景简化代码)
      - [交换两个变量值](#交换两个变量值)
      - [快速获取当前页面的域名和路径](#快速获取当前页面的域名和路径)
      - [函数接收对象参数，直接把对象的属性绑定到变量](#函数接收对象参数直接把对象的属性绑定到变量)

<!-- /TOC -->

## 1.解构赋值

### 1.1 使用解构赋值，快速获取对象的指定属性：

其中`var {name, age, passport} = person;`这种写法就源于此

```
'use strict';

var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school'
};
var {name, age, passport} = person;  // * 这东西原来是es6里面的解构赋值 *

// name, age, passport分别被赋值为对应属性:
console.log('name = ' + name + ', age = ' + age + ', passport = ' + passport); // name = 小明, age = 20, passport = G-12345678

```

### 1.2 对嵌套对象属性赋值

>注意：如果对应的属性不存在，变量将被赋值为undefined

```
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school',
    address: {
        city: 'Beijing',
        street: 'No.1 Road',
        zipcode: '100001'
    }
};
var {name, address: {city, zip}} = person;
name; // '小明'
city; // 'Beijing'
zip; // undefined, 因为属性名是zipcode而不是zip
// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
address; // Uncaught ReferenceError: address is not defined
```

### 1.3 利用新变量替代属性名

```
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school'
};

// 把passport属性赋值给变量id:
let {name, passport:id} = person;
name; // '小明'
id; // 'G-12345678'
// 注意: passport不是变量，而是为了让变量id获得passport属性:
passport; // Uncaught ReferenceError: passport is not defined
```

### 1.4 使用默认值

>主要避免不存在的属性返回undefined

```
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678'
};

// 如果person对象没有single属性，默认赋值为true:
var {name, single=true} = person;
name; // '小明'
single; // true
```

### 1.5 有时候正确写法报错

有些时候，如果变量已经被声明了，再次赋值的时候，正确的写法也会报语法错误:

```
// 声明变量:
var x, y;
// 解构赋值:
{x, y} = { name: '小明', x: 100, y: 200};
// 语法错误: Uncaught SyntaxError: Unexpected token =
```

这是因为JavaScript引擎把{开头的语句当作了块处理，于是=不再合法。解决方法是用小括号括起来:

```
({x, y} = { name: '小明', x: 100, y: 200});
```

### 1.6 使用场景，简化代码

#### 交换两个变量值

```
var x=1, y=2;
[x, y] = [y, x]
```

#### 快速获取当前页面的域名和路径

```
var {hostname:domain, pathname:path} = location;
```

#### 函数接收对象参数，直接把对象的属性绑定到变量

```
function buildDate({year, month, day, hour=0, minute=0, second=0}) {
    return new Date(year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second);
}
```

>注意：使用解构赋值可以减少代码量，但是，需要在支持ES6解构赋值特性的现代浏览器中才能正常运行。目前支持解构赋值的浏览器包括Chrome，Firefox，Edge等。

详情可参考如下：

[es6入门](http://es6.ruanyifeng.com/?search=rest&x=0&y=0#docs/destructuring)

[廖雪峰js教程](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014344993159773a464f34e1724700a6d5dd9e235ceb7c000)


