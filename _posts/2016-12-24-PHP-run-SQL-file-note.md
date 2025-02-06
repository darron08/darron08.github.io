---
layout: post
title:  "执行SQL文件"
date:   2016-12-24
categories: PHP
tags:  PHP
author: darron
---

* content
{:toc}

项目中要实现一个打补丁功能，需要加载一个SQL文件并执行其中的所有语句，看了很多网友的意见，并复习了一遍正则表达式，最终实现了。现在记录一下我的方法。

## 代码

```php

	//步骤一
	$pattern = array(
            '/^\x{EF}\x{BB}\x{BF}/',
            "/\r\n\x{EF}\x{BB}\x{BF}/",
            "/\n\x{EF}\x{BB}\x{BF}/",
            "/\r\x{EF}\x{BB}\x{BF}/",
            '/\/\*.*\*\//Us',
            "/--.*\r\n/",
            "/--.*\n/",
            "/--.*\r/"
            );

	$subject = preg_replace($pattern, PHP_EOL, $subject);
	
	//步骤二：把所有换行符换成空字符串
	$subject = str_replace(array("\r\n", "\n", "\r"), '', trim($subject));
        
	//步骤三：去掉尾部的';'
	$subject=rtrim($subject,';');
    
	//步骤四：拆分SQL文件，生成一个数组（后续便可以循环执行每一条语句）
	$sqls=explode(";",$subject);

```

## 详细说明

这里主要说一下步骤一，步骤一是用来清除一些注释等没用的信息。

* $pattern[0] 到 $pattern[3] 都是为了去除BOM头，因为我的SQL文件几乎都会来自windows，而且可能是由几个人拼接起来的，所以一个SQL文件里面可能会很多行都有BOM头。此处之外，我还保证了不论这个文件是来自windows，unix还是ios，都可以清除所有的BOM；

* $pattern[4]是为了清除C语言风格的多行注释；

* $pattern[5] 到 $pattern[7] 是为了清除SQL注释（无论这个SQL文件来自哪个平台）。


有关BOM的介绍可以看看该链接：[https://www.zhihu.com/question/20167122](https://www.zhihu.com/question/20167122)