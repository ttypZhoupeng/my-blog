---
layout: post
title: js基础篇-3.高阶函数
subtitle: HOF
date: 2019-04-15
author: "zhoupeng"
header-img: "img/post-bg-js-version.jpg"
tags: js基础篇
---

# js基础查漏补缺

<!-- TOC -->

- [js基础查漏补缺](#js%E5%9F%BA%E7%A1%80%E6%9F%A5%E6%BC%8F%E8%A1%A5%E7%BC%BA)
  - [高阶函数](#%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0)
    - [Map](#map)
    - [reduce](#reduce)
    - [filter](#filter)
      - [回调函数](#%E5%9B%9E%E8%B0%83%E5%87%BD%E6%95%B0)
    - [sort](#sort)
      - [最后](#%E6%9C%80%E5%90%8E)

<!-- /TOC -->

## 高阶函数

JavaScript的函数其实都指向某个变量。既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。

一个最简单的高阶函数： 

```
function add(x, y, f) {
    return f(x) + f(y);
}
```

```
function add(x, y, f) {
    return f(x) + f(y);
}

var x = add(-5, 6, Math.abs); // 11
console.log(x);

```

### Map

数组Array的一个方法，与es6 Map有所不同

```
function pow(x) {
    return x * x;
}

var results = arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
console.log(results);
```

### reduce

reduce也是Array的一个方法，这个函数必须接收两个参数，reduce()把结果继续和序列的下一个元素做累积计算。

对一个array求和：

```
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x + y;
}); // 25
```

对一个array求积：

```
function product(arr) {
 const newArr =  arr.reduce(function (x, y) {
    return (x * y);
});

return newArr
}
```

把[1, 3, 5, 7, 9]变换成整数13579：

```
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x * 10 + y;
}); 
```

### filter

filter()也接收一个函数。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是true还是false决定保留还是丢弃该元素

删除array中偶数：

```
var arr = [1, 2, 4, 5, 6, 9, 10, 15];
var r = arr.filter(function (x) {
    return x % 2 !== 0;
});
r; // [1, 5, 9, 15]
```

删除array中空字符串：

```
var arr = ['A', '', 'B', null, undefined, 'C', '  '];
var r = arr.filter(function (s) {
    return s && s.trim(); // 注意：IE9以下的版本没有trim()方法
});
r; // ['A', 'B', 'C']
```

#### 回调函数

```
var arr = ['A', 'B', 'C'];
var r = arr.filter(function (element, index, self) {
    console.log(element); // 依次打印'A', 'B', 'C'
    console.log(index); // 依次打印0, 1, 2
    console.log(self); // self就是变量arr
    return true;
});
```

去除array重复元素：

```
var
    r,
    arr = ['apple', 'strawberry', 'banana', 'pear', 'apple', 'orange', 'orange', 'strawberry'];
r = arr.filter(function (element, index, self) {
    return self.indexOf(element) === index;
});

console.log(r.toString());
```

### sort 

通常规定，对于两个元素x和y，如果认为x < y，则返回-1，如果认为x == y，则返回0，如果认为x > y，则返回1

```
// 看上去正常的结果:
['Google', 'Apple', 'Microsoft'].sort(); // ['Apple', 'Google', 'Microsoft'];

// apple排在了最后:
['Google', 'apple', 'Microsoft'].sort(); // ['Google', 'Microsoft", 'apple']

// 无法理解的结果(这里是个坑):
[10, 20, 1, 2].sort(); // [1, 10, 2, 20]
```

第二个排序把apple排在了最后，是因为字符串根据ASCII码进行排序，而小写字母a的ASCII码在大写字母之后。

第三个排序结果是什么鬼？简单的数字排序都能错？

这是因为Array的sort()方法默认把所有元素先转换为String再排序，结果'10'排在了'2'的前面，因为字符'1'比字符'2'的ASCII码小。

要按数字大小排序，我们可以这么写：

```
var arr = [10, 20, 1, 2];
arr.sort(function (x, y) {
    if (x < y) {
        return -1;
    }
    if (x > y) {
        return 1;
    }
    return 0;
});
console.log(arr); // [1, 2, 10, 20]
```

详情可参考如下：

[廖雪峰js教程](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434499355829ead974e550644e2ebd9fd8bb1b0dd721000)

#### 最后

感谢各位大佬的参阅，如果有什么问题都可以在评论区提出。

如果觉得对您有帮助的话，也可点个赞，点个收藏，点关注不迷路。

转载请注明：[周鹏的博客](https://ttypzhoupeng.github.io/my-blog) » [点击阅读原文](https://ttypzhoupeng.github.io/my-blog/2019/04/15/higher_order_functions/)
