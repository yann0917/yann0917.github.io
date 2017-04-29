---
title: PHP基础-函数
date: 2017-04-29 13:26:19
tags: php
categories: php
---


> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25


### 函数参数

#### Example-1

> - 自PHP 5.0 支持参数类型声明为对象类型，即参数必须是某个对象的类；
> - 自PHP 5.1 新增参数类型声明为array，即形参类型必须是数组；
> - 自PHP 7.0 新增函数参数类型为标量类型，同时参数类型声明支持严格模式，此时只支持标量类型`int string float bool`。


```php
<?php
	//PHP 7.0 
	declare(strict_types=1); //声明严格类型
	
	function sum(int $a, int $b) { 
	    return $a + $b;
	}
	
	var_dump(sum(1, 2));
	var_dump(sum(1.5, 2.5)); //类型错误
 
```
#### Example-2

> 方法的参数类型约束为对象类型：

```php
<?php
	class C {}
	class D extends C {}

	// This doesn't extend C.
	class E {}

	function f(C $c) {
	    echo get_class($c)."\n";
	}

	f(new C);
	f(new D);
	f(new E); //错误，参数必须是C的实例

```
#### Example-3

> PHP5.6+版本支持可变数量的参数列表。在 PHP 5.5 及更早版本中，使用函数 func_num_args()，func_get_arg()，和 func_get_args() 。 

```php
<?php
	function sum(...$numbers) {
	    $accum = 0;
	    foreach ($numbers as $n) {
	        $accum += $n;
	    }
	    return $accum;
	}

	echo sum(1, 2, 3, 4);
```
#### Example-4
> 使用默认参数时，任何默认参数必须放在任何非默认参数的右侧；否则，函数将不会按照预期的情况工作。

```php
<?php
	function makeyogurt($type = "acidophilus", $flavour){
	    return "Making a bowl of $type $flavour.\n";
	}
	
	echo makeyogurt("raspberry");   // won't work as expected

```
### 可变函数
可变函数不能用于例如 echo，print，unset()，isset()，empty()，include，require 以及类似的语言结构。
#### Example-1
> 调用静态方法时，函数调用要比静态属性优先。


```php
<?php
class Foo
{
    static $variable = 'static property';
    static function Variable()
    {
        echo 'Method Variable called';
    }
}

echo Foo::$variable; // This prints 'static property'. 
$variable = "Variable";
Foo::$variable();  // This calls $foo->Variable() 


```
#### Example-2
> PHP 7.0 支持类调用方法为可变方法，而之前的版本则报错。

```php
<?php
namespace Project\TestClass;
 class Test {
     static function funcToCall() {
         echo "test";
     }
 }

namespace Project\OtherTestClass;
 class OtherTest {
     static function callOtherFunc() {
         $func = '\Project\TestClass\Test::funcToCall';
         $func();
     }
 }
 
 echo OtherTest::callOtherFunc(); //outputs test

```

### 匿名函数

匿名函数（Anonymous functions），也叫闭包函数（closures），允许临时创建一个没有指定名称的函数。最经常用作回调函数（callback）参数的值。

#### Example-1
> 闭包函数也可以作为变量的值来使用。PHP 会自动把此种表达式转换成内置类 Closure 的对象实例。


```php
<?php
	//PHP 7.0+
     (function($name){
         echo 'My name is ' . $name;
     })('Zhang San');

```
#### Example-2
> 闭包可以从父作用域中继承变量。 任何此类变量都应该用 use 语言结构传递进去。


```php
<?php
	$fruits = ['apples', 'oranges'];

	$example = function () use ($fruits) {
	    echo $fruits[0]; 
	};
	$example();

```
#### Example-3

> PHP 5.4 `$this`可用于匿名函数。

```php
<?php

	class Test{
	    public function testing(){
	        return function() {
	            var_dump($this);
	        };
	    }
	}
	
	$object = new Test;
	$function = $object->testing();
	$function();
    
```
### Return
> - 如果在全局范围中调用，则当前脚本文件中止运行。
> - 如果当前脚本文件是被 include 的或者 require 的，则控制交回调用文件。
> - 如果当前脚本是被 include 的，则 return 的值会被当作 include 调用的返回值。
> - 如果在主脚本文件中调用 return，则脚本中止运行。
> - 如果当前脚本文件是在 php.ini 中的配置选项 auto_prepend_file 或者 auto_append_file 所指定的，则此脚本文件中止运行。

#### Example-1

> PHP 7.0 可以强制函数返回类型。语法为` function FunctionName ($arg1, $arg2, ...)  : TYPE { } `


```php
<?php
	declare(strict_types=1);   //strict_types

	function welcome($name):string {
	     return $name;
	}
	echo welcome(90.99);

```
```php
<?php
	class C {}

	function getC(): C {
	    return new C;
	}

	var_dump(getC()); //object(C)#1 (0) { }
 
```
#### Example-2
> PHP 7.0 如果指定了返回类型，则不能为null.


```php
<?php
declare(strict_types=1);

function add2ints(int $x, int $y):int{
    $z = $x + $y;
    if ($z===0){
       return null; //返回类型错误
    }
    return $z;
}
$a = add2ints(3, 4);
echo is_null($a) ? 'Null' : $a;
$b = add2ints(-2, 2);
echo is_null($b) ? 'Null' : $b;
exit();
```
---
