# 引用是什么

在 PHP 中引用意味着用不同的名字访问同一个变量内容。这并不像 C 的指针：例如你不能对他们做指针运算，他们并不是实际的内存地址。 替代的是，引用是符号表别名。注意在PHP 中，变量名和变量内容是不一样的， 因此同样的内容可以有不同的名字。最接近的比喻是 Unix 的文件名和文件本身——变量名是目录条目，而变量内容则是文件本身。引用可以被看作是 Unix 文件系统中的硬链接。



# 引用做什么

PHP 的引用允许用两个变量来指向同一个内容。意思是，当这样做时：

```php
$a =& $b;
```

这意味着 $a 和 $b 指向了同一个变量。

注意：$a 和 $b 在这里是完全相同的，这并不是 $a 指向了 $b 或者相反，而是 $a 和 $b 指向了同一个地方。它们都使用了同一个变量容器（又名：zval）。将这两者分开的唯一方法是使用unset()函数销毁其中任何一个变量。



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



# 引用传递

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



# 引用返回

引用返回用在当想用函数找到引用应该被绑定在哪一个变量上面时。*不要*用返回引用来增加性能，引擎足够聪明来自己进行优化。仅在有合理的技术原因时才返回引用！(说实话，没看懂。。。)

```php
class foo {
    public $value = 42;

    public function &getValue() {
        return $this->value;
    }
}

$obj = new foo;
$myValue = &$obj->getValue(); // $myValue is a reference to $obj->value, which is 42.
$obj->value = 2;
echo $myValue;                // prints the new value of $obj->value, i.e. 2.
```

**Note**: 和参数传递不同，这里必须在两个地方都用 *&* 符号——指出返回的是一个引用，而不是通常的一个拷贝，同样也指出 $myValue 是作为引用的绑定，而不是通常的赋值。



# 引用不是什么

如前所述，引用不是指针。这意味着下面的结构不会产生预期的效果(输出100)，而是会输出1：

```php
$bar = 1;
$GLOBALS['baz'] = 100;
function foo(&$var)
{
    $var =& $GLOBALS["baz"];
}
foo($bar);
var_dump($bar);	//int(1)
```

这将使foo函数中的$var变量在函数调用时和$bar绑定在一起，但接着又被重新绑定到了$GLOBAL["baz"]上面。不可能通过引用机制将$bar在函数调用内绑定到别的变量上面，因为在函数foo中并没有变量$bar（它被表示为$var，但是$var只有变量内容而没有调用符号表中的名字到值的绑定）。



我感觉上面这个例子让我看得有点迷惘，以下例子可能会比较好：

```php
$b = 100;
$a =& $b;

$c = 10;
$a =& $c;	//$a跟$b解除绑定，并绑定到$c上

var_dump($a);	//int(10)
var_dump($b);	//int(100)
var_dump($c);	//int(10)

$c = 123;
$b = 456;

var_dump($a);	//int(123)
var_dump($b);	//int(456)
var_dump($c);	//int(123)
```

修改$c和$b的值后，发现$a是跟随$c改变的。



注意和下面的区别：

```php
$b = 100;
$a =& $b;

$c = 10;
$c =& $a;	//$c也绑定到$a上，于是$a，$b和$c绑定到一起

var_dump($a);	//int(100)
var_dump($b);	//int(100)
var_dump($c);	//int(100)

$c = 1;

var_dump($a);	//int(1)
var_dump($b);	//int(1)
var_dump($c);	//int(1)
```

修改$c的值后，$a，$b和$c都改变了。



# 取消引用

当 unset 一个引用，只是断开了变量名和变量内容之间的绑定。这并不意味着变量内容被销毁了。例如：

```php
$a = 1;
$b =& $a;
unset($a);
```

不会 unset $b，只是$a。

再拿这个和 Unix 的 **unlink** 调用来类比一下可能有助于理解。



# 引用定位

许多 PHP 的语法结构是通过引用机制实现的，所以上述有关引用绑定的一切也都适用于这些结构。一些结构，例如引用传递和返回，已经在上面提到了。其它使用引用的结构有：



## *global* 引用

当用 **global $var** 声明一个变量时实际上建立了一个到全局变量的引用。也就是说和这样做是相同的：

```php
$var =& $GLOBALS["var"];
```

这意味着，unset $var 不会 unset 全局变量。



## $this

在一个对象的方法中，$this 永远是调用它的对象的引用。



to read: 对象和引用