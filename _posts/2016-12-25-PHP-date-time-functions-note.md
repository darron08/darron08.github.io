---
layout: post
title:  "PHP日期时间函数的学习记录"
date:   2016-12-25
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

## date()函数的应用

### date()函数是不能显示中文的，那么怎么用中文显示星期几呢？

例一:

```php
$day = date('w');

switch($day){
	case 0: $dayStr = '日';break;
	case 1: $dayStr = '一';break;
	case 2: $dayStr = '二';break;
	case 3: $dayStr = '三';break;
	case 4: $dayStr = '四';break;
	case 5: $dayStr = '五';break;
	case 6: $dayStr = '六';break;
}

echo date('Y年m月d日')."星期$dayStr";
```

例二:

```php
$week = array('星期日','星期一','星期二','星期三','星期四','星期五','星期六');
echo date('Y年m月d日 H:i:s ').$week[date('w')];
```
### 判断是否为闰年Leap Year

- 方法一：查看2月份有多少天，大于28则为闰年

- 方法二：闰年算法

四年一闰，百年不闰，四百年再闰  

```php
$year = date('Y');

if ( $year%4==0 &&  $year%100!=0 || $year%400==0  ){
	echo $year.'是闰年';
}else{
	echo $year.'不是闰年';
}
```

- 方法三：使用date()的参数'L'

date('L'): 是否为闰年，如果是闰年返回1，否则返回0.

## 常见笔试题：计算两个日期的时间差
```php
$birth = mktime(0, 0, 0, 6, 1, 1990); //计算出生时间的时间戳，假设是1990年6月1日出生
$time = time();
$age = floor(($time - $birth)/(24*3600*365)); //计算年龄
```