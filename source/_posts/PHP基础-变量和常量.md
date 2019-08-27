---
title: PHP基础-变量和常量
tags: php
categories: php
abbrlink: 37653
date: 2017-04-28 23:51:17
---

> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

- PHP 中的变量用一个美元符号`$`后面跟变量名来表示。变量名**区分大小写**；
- 变量名与 PHP 中其它的标签一样遵循相同的规则。一个有效的变量名由字母或者下划线开头，后面跟上任意数量的字母，数字，或者下划线。按照正常的正则表达式，它将被表述为：`[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*`；
- PHP 中全局变量在函数中使用时必须声明为 global；
- 静态变量仅在局部函数域中存在，但当程序执行离开此作用域时，其值并不丢失；

### 变量基础

#### Example-1

> 不能为$this关键字赋值

```php
<?php
	//$this = 'text'; // error:Cann't re-assign $this
	$name = 'this';
	$$name = 'text'; // sets $this to 'text',在PHP 5.6.29上可运行，在PHP 7.1.0上依然是错误
	echo $this;

```

#### Example-2

> 使用异或操作可以在不增加临时变量的情况下互相交换变量值，对integer和string型变量有效，数组和对象无效。


```php
<?PHP
	$a=5;
	$b=3;
	//使用异或(XOR)可以在不添加临时变量的情况下，交换两个变量的数据。
	//以下代码会交换$a,$b的值。
	$a^=$b;
	$b^=$a;
	$a^=$b;
	echo $a.PHP_EOL.$b;

```

#### Example-3

> unset()销毁局部变量$a，全局变量$a仍然存在，同理unset()销毁全局变量$a，局部变量仍然存在。
> 使用NULL将局部变量置空，则局部和全局变量均为NULL.

```php
<?php
 $var = 1;
 function foo() {
     global $var;
     unset($var);               // unset local $a, the global $a is still there.
     var_dump($var);            // Undefined variable: var
     var_dump($GLOBALS['var']); // this is ok.
}
foo();
var_dump($var);                // this is ok.

```

#### Example-4

> 销毁类的实例化对象的时候，类的静态方法内部的静态变量并不会被销毁。


```php
<?php
class Foo {
     public static function Bar() {
         static $var = 0;
         return ++ $var;
     }
 }

$Foo_instance = new Foo;
print $Foo_instance->Bar(); // prints 1
unset($Foo_instance);
echo "<br/>";
$Foo_instance2 = new Foo;
print $Foo_instance2->Bar(); // prints 2

```

#### Example-5

> 在函数嵌套的结构里，函数的参数相对于内部函数的变量并不是全局变量


```php
<?php
// $var1 is not declared in the global scope
function a($var1){
     function b(){
         global $var1;
         echo $var1; // there is no var1 in the global scope so nothing to echo 
     }
     b();
 }
a('hello');

```

### 可变变量

> 一个可变变量获取了一个普通变量的值作为这个可变变量的变量名。

#### Example-1

> 在可变变量中，可以使用花括号来给变量名清晰定界。


```php
<?php
	//在可变变量中，使用花括号来给属性名清晰定界
	$a = 'hello';
	$$a = 'world';
	echo "$a ${$a}"; //等价于 echo "$a $hello";

```

#### Example-2

> 可以在变量前添加更多的$符号。


```php
<?php
	$I = 'love';
	$love = 'you';
	$you = 'I';
	echo $$$I." ".$$$$I." ".$$I;

```

#### Example-3

> 可变变量$符号之间有空格、注释时，可变变量仍然可以被合法解析


```php
<?php
    $foo = 'bar';
    $ 
    /**
     * 可变变量$符号之间有空格、注释，可变变量仍然可以被合法解析.
     */
    $foo = 'magic';
    echo $bar; // Outputs magic.

```

#### Example-4

> 类中的可变属性如果为数组，其默认解析规则在PHP 5 和PHP 7中有所不同，为了解决版本兼容问题，建议使用{}给属性名清晰定界。


```php
<?php
	class foo {
	    public $bar = 'I am bar.';
	    public $arr = array('I am A.', 'I am B.', 'I am C.');
	    public $r   = 'I am r.';
	}
	$foo = new foo();
	$bar = 'bar';
	$baz = array('foo', 'bar', 'baz', 'quux');
	echo $foo->$bar . "<br/>\n";
	echo $foo->$baz[1]. "<br/>\n"; 
	//PHP 5 中按 $foo->{$baz[1]}解析，
	//PHP 7中按$foo->{$baz}[1]解析，会通知未定义属性 foo::$Array

	$start = 'b';
	$end   = 'ar';
	echo $foo->{$start . $end} . "<br/>\n";

	$arr = 'arr';
	echo $foo->{$arr}[1] . "<br/>\n"; 
	//PHP 5 中按$foo->{$arr[1]}解析成$foo->r,
	//PHP 7 中按$foo->{$arr}[1]解析为$foo->arr[1]
	echo $foo->{$arr}[1] . "<br/>\n";

```

#### Example-5

> 可以使用变量、常量、函数调用拼接成另一个变量，但是不能拼接函数名。


```php
<?php 
	define('ONE', 1); 
	function one() { 
	    return 1; 
	} 
	$one = 1; 

	${"foo$one"} = 'foo'; 
	echo $foo1,"<br/>"; // foo 
	${'foo' . ONE} = 'bar'; 
	echo $foo1,"<br/>"; // bar 
	${'foo' . one()} = 'baz'; 
	echo $foo1,"<br/>"; // baz 

	$foo = 'info'; 
	//{"php$foo"}(); // 解析错误 
	// You'll have to do: 
	$func = "php$foo"; 
	$func(); 
 
```

### 来自PHP之外的变量

#### Example-1

> 来自表单中的变量，变量名中的点和空格被转换成下划线。例如 `<input name="a.b" />` 变成了 `$_REQUEST["a_b"]`。

```html
<form action='./test.php' method='POST'>
	<input name="a. b[x. y]" value="foo" />
	<input type="submit" name="submit" />
</form>
```

> test.php文件如下

```php
<?php
	var_dump($_POST);

```

### 常量

> 常量是一个简单值的标识符（名字）。如同其名称所暗示的，在脚本执行期间该值不能改变（除了所谓的魔术常量，它们其实不是常量）。常量默认为大小写敏感，一般常量标识符总是大写的。
>
>常量名和其它任何 PHP 标签遵循同样的命名规则。合法的常量名以字母或下划线开始，后面跟着任何字母，数字或下划线。用正则表达式是这样表达的：`[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*`。
>
>PHP 5.3.0之后新增`const`关键字定义常量；

#### Example-1

> 定义常量时，常量名不可以有空格。


```php
<?php
   define ("YEAR ",  2017);
   echo YEAR;


```

#### Example-2

> PHP 7.1.0 之后const可以显式使用访问控制修饰符，在此之前不能使用访问控制修饰符。

```php
<?php
class constants {
    const MAX_VALUE = 10;
    public const MIN_VALUE =1;
 }
echo constants::MAX_VALUE;
echo constants::MIN_VALUE; //PHP 7.1.0 之后可显式写访问控制修饰符

```

#### Example-3

> 使用define()定义常量时，可以使用内置关键字声明常量，使用constant()返回常量值，但是使用const定义常量则会报错，不能重新声明常量。


```php
<?php
	define('TRUE','My constant value');
	echo constant('TRUE'); // outputs 'My constant value'

	//const ECHO = 'My constant value ECHO'; //致命错误，不能重新声明常量
	//echo ECHO;

```
有关const和define()定义常量的区别，参见[PHP定义常量时const关键字和define()函数的区别](http://blog.csdn.net/abc1991234/article/details/55000492).

---
