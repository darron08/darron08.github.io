---
layout: post
title:  "JS判断变量是否为空"
date:   2017-06-24
categories: JavaScript
tags:  JavaScript
author: Tse
---

* content
{:toc}

## ECMAScript类型

先了解一下ECMAScript的变量类型。ECMAScript中有5种基本数据类型：Undefined, Null, Boolen, Number, String。还有一种复杂数据类型： Object。

## typeof操作符

对一个值使用typeof操作符可能会返回下列某个字符串：  

- "undefined" --- 如果这个值未定义（也就是undefined)或者是一个未声明过的变量；
- "boolean" --- 如果这个值是布尔值；
- "string" --- 如果这个值是字符串；
- "number" --- 如果这个值是数值；
- "object" --- 如果这个值是对象或null；
- "function" --- 如果这个值是函数。

可以发现，typeof的返回值虽然也是有6种，但它们跟ECMAScipt的数据类型并不是一一对应的！  
比如，调用typeof null会返回"object"，因为特殊值null被认为是一个空的对象引用。  
如果定义的变量准备在将来用来保存对象，那么最好将该变量初始化为null而不是其他值。这样一来，只要直接检查null值就可以知道相应的变量是否已经保存了一个对象的引用(不一定要这么做，视情况而定)，如下面例子： 

```js  
	if (car!=null){
		//对car对象执行某些操作
	}
```

## if (val) 和 Boolean()

在很多语言中，都可以用 if (变量) 来判断一个变量是否存在或是否为空。那么在ECMAScript中是否可以这样呢？要知道答案，必须要知道各类型转换为Boolean类型的规则，因为if (变量) 语句会隐式执行Boolean()。  
### String  
- 任何非空字符串 -> true;   
- 空字符串 -> false;  

### Number  
- 任何非零数字值（包括无穷大）-> true  
- 0和NaN -> false

### Object  
- 任何对象 -> true  
- null -> false  

### Undefined
- 只有false

## 判断变量是否为空

### String
针对表单验证，因为是String类型，所以判断内容是否为空很简单，例如：
```js  
	var name = $('#name').val();
	if (!name)
		alert('名字不能为空!');
```

### Object
针对对象和数组类型，就不能这样判断了，因为任何对象(数组也是对象)转换为Boolean类型，其值都是true
 


