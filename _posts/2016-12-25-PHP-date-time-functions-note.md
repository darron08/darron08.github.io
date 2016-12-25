---
layout: post
title:  "PHP日期时间函数的学习记录"
categories: PHP
tags:  PHP
author: Tse 
---

* content
{:toc}

## 如何获取上个月的时间

```php
//输出上个月的最后一天的时间
echo date('Y-m-d H:i:s', strtotime('last day of privious month')).'<br/>';

//输出上个月的第一天的时间
echo date('Y-m-d H:i:s', strtotime('first day of previous month')).'<br/>';

//输出上上个月的最后一天的时间，根据'-'号后面的数字来决定是哪一个月
echo date('Y-m-d H:i:s', strtotime('last day of -2 months')).'<br/>';

//输出上个月的第一天的时间，在PHP中，每个月的第一天的昨天就是上个月的最后一天
echo date('Y-m-d H:i:s', strtotime('yesterday', strtotime('first day of this month'))).'<br/>';

/*
	注意，以下这两种写法得到的结果是不可靠的。
	这里意图是想获取上个月的时间，但如果这个月的天数比上个月的天数多，很可能就会出错，
	因为'previous month'和'last month'是按照固定天数去减的，并不是真正的上个月！
*/
echo date('Y-m-d H:i:s', strtotime('previous month')).'<br/>';
echo date('Y-m-d H:i:s', strtotime('last month'));
```