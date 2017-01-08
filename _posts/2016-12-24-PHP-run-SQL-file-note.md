---
layout: post
title:  "执行SQL文件"
date:   2016-12-24
categories: PHP
tags:  PHP
author: Tse
---

* content
{:toc}

项目中需要实现一个打补丁功能，需要加载一个SQL文件并执行其中的所有语句，看了很多网友的意见，并复习了一遍正则表达式，最终实现了。现在记录一下我的方法。

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
	
	//步骤二
	$subject = str_replace(array("\r\n", "\n", "\r"), '', trim($subject));
        
	//步骤三，去掉尾部的;
	$subject=rtrim($subject,';');
    
	//步骤四
	$sqls=explode(";",$subject);

```

## 详细说明

步骤一：这里是为了去除一些注释等没用的信息