---
title: PHP基础-流程控制
date: 2017-04-29 13:23:58
tags: php
categories: php
---

> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

### 流程控制语句替代语法
替代语法的基本形式是把左花括号（{）换成冒号（:），把右花括号（}）分别换成 endif;，endwhile;，endfor;，endforeach; 以及 endswitch;。 

#### Example-1

> 不可以在一个控制模块里混用两种流程控制语法。

```php
<?php
$a =2;
 if($a):
     echo $a;
    if($b) {
       echo $b;
    }
else: //解析错误
    echo $c; 
 endif;
 
```
#### Example-2

> 替代语法也可以写成如下形式，方便在混编代码中随意循环HTML元素。


```php
<?php for( $a = 0; $a < 5; $a++ ) { ?>
 //可随意循环HTML元素
<?php }; ?>
```
#### Example-3

> 只有do while循环没有替代语法。

```php
<?php
	//代码解析错误
	do : 
	     $a++
	 while ($a <= 10);
 
```
### 循环控制语句

#### Example-1

> for()循环的简写形式：


```php
<?php
	for ($i = 1, $j = 0; $i <= 10; $j += $i, print $i, $i++);
 
```
#### Example-2

> 请注意for循环表达式的执行顺序：expr1在循环开始前无条件求值（并执行）一次；expr2 在每次循环开始前求值；expr3 在每次循环之后被求值（并执行）。


```php
<?php
	 $array = array(0 => "a", 1 => "b", 2 => "c", 3 => "d");
	 for($i = 0; $i < count($array); $i++){
	 echo $array[$i]; //outputs ab
	 unset($array[$i]);
 }

```
#### Example-3

> foreach 不支持用"@"来抑制错误信息的能力。
> PHP 5.5 增添了遍历一个数组的数组的功能并且把嵌套的数组解包到循环变量中，只需将 list() 作为值提供。list() 中的单元可以少于嵌套数组的，此时多出来的数组单元将被忽略。

```php
<?php
	$array = [
	    [1, 2],
	    [3, 4],
	];
	foreach ($array as list($a, $b)) {
	    echo "A: $a; B: $b\n";  // A: 1; B: 2 A: 3; B: 4
	}

```
#### Example-4

> 如果想遍历多维数组，list()函数可嵌套使用。

```php
<?php
	$array = [
	    [1, 2, array(3, array(4, 5))],
	    [3, 4, array(5, array(6, 7))],
	];

	foreach ($array as list($a, $b, list($c, list($d, $e)))) {
	    echo "A: $a; B: $b; C: $c; D: $d; E: $e;<br>";
	}

```
#### Example-5

> 关联数组的索引如果是由float转换过来的字符串，其索引仍然是string类型，如果是int型转换过来的字符串，其索引会强制转换成int。

```php
<?php
	$arr = array();
	$arr[0] = "zero";            // will stay an int
	$arr["1"] = "one";            // will be cast to an int !
	$arr["two"] = "2";            // will stay a string
	$arr["3.5"] = "threeandahalf";    // will stay a string

	foreach($arr as $key => $value) {
	    var_dump($key);
	}

```
#### Example-6
> PHP 7 以下代码会报错，因为break不在循环或者switch语句中，而PHP 7 之前的版本则不会报错。


```php
<?php
	function a() {
		if (1 === 1) {
		break;
		}
		echo 'something';
	}


```

---
