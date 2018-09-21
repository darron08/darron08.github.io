# 引用是什么

在 PHP 中引用意味着用不同的名字访问同一个变量内容。这并不像 C 的指针：例如你不能对他们做指针运算，他们并不是实际的内存地址。 替代的是，引用是符号表别名。注意在PHP 中，变量名和变量内容是不一样的， 因此同样的内容可以有不同的名字。最接近的比喻是 Unix 的文件名和文件本身——变量名是目录条目，而变量内容则是文件本身。引用可以被看作是 Unix 文件系统中的硬链接。



# 引用做什么

PHP 的引用允许用两个变量来指向同一个内容。意思是，当这样做时：

```php
$a =& $b;
```

这意味着 $a 和 $b 指向了同一个变量。

注意：$a 和 $b 在这里是完全相同的，这并不是 $a 指向了 $b 或者相反，而是 $a 和 $b 指向了同一个地方。



好了，上面都是摘自php手册的。但我觉得这个例子并不够完整

```php
$b = 100;
$a =& $b;
var_dump($a);	//打印100
var_dump($b);	//打印100
```

$a 和 $b 确实是完全相同的。

```php
$a = 100;
$a =& $b;
var_dump($a);	//打印NULL
var_dump($b);	//打印NULL
```

这个例子里，$a和$b也是完全相同的，但是因为是$b赋值给$a，所以$a的值是NULL。



```php
$b = 100;
$a =& $b;
$c = $a;
$c = 10;
var_dump($a);	//打印100
var_dump($b);	//打印100
var_dump($c);	//打印10
```

$a赋值给$c，$c的值改变并不会影响$a。



继续摘抄PHP手册！

注：如果具有引用的数组被拷贝，其值不会解除引用。对于数组传值给函数也是如此。

这句话有毒吧。

```php
$b = array(1,2,3);
$a =& $b;
$c = $a;
$c = array(1);
var_dump($a);
var_dump($b);
var_dump($c);
```

经过测试，$a的值拷贝到$c，然后$c的值被修改了，但是$a和$b的值没有变，说明其值是会解除引用的！后面看了英文版的php手册，是没有这句话的，可能是旧版PHP才有的特性吧？



如果对一个未定义的变量进行引用赋值、引用参数传递或引用返回，则会自动创建该变量。

```php
function foo(&$var) { }
foo($a); // $a is "created" and assigned to null
var_dump($a);

$b = array();
foo($b['b']);
var_dump(array_key_exists('b', $b)); // bool(true)

$c = new StdClass;
foo($c->d);
var_dump(property_exists($c, 'd')); // bool(true)
```



同样的语法可以用在函数中，它返回引用

```php
$foo =& find_var($bar);
```



引用做的第二件事是用引用传递变量。这是通过在函数内建立一个本地变量并且该变量在呼叫范围内引用了同一个内容来实现的。例如：

```php
function foo(&$var)
{
    $var++;
}
$a=5;
foo($a);
```

将使 $a 变成 6。这是因为在 foo 函数中变量 $var 指向了和 $a 指向的同一个内容。



# 引用不是什么



```php
$b = 100;
$a =& $b;

var_dump($a);
var_dump($b);


$c = 10;
$a =& $c;

var_dump($a);
var_dump($b);
var_dump($c);
```

打印100，100，10，100，10



```php
$b = 100;
$a =& $b;

var_dump($a);
var_dump($b);


$c = 10;
$c =& $a;

var_dump($a);
var_dump($b);
var_dump($c);

$c = 1;

var_dump($a);
var_dump($b);
var_dump($c);
```

int(100) int(100) int(100) int(100) int(100) int(1) int(1) int(1)





要解释一下什么是引用赋值，引用参数传递，引用返回！

to read: 对象和引用