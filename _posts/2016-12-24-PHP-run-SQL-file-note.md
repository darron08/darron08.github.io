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

项目中需要加载一个SQL文件并执行其中的所有语句，看了很多网友的意见，并复习了一遍正则表达式，最终实现了。现在记录一下我的方法。

```php

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

        $subject = str_replace(array("\r\n", "\n", "\r"), '', trim($subject));
        
        //去掉尾部的;
        $subject=rtrim($subject,';');
        
        $sqls=explode(";",$subject);

```