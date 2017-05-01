---
title: PHP基础-表达式
tags: php
categories: php
abbrlink: 59089
date: 2017-04-29 13:18:54
---


> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

### 表达式
在 PHP 中，几乎所写的任何东西都是一个表达式。简单但却最精确的定义一个表达式的方式就是“**任何有值的东西**”。


#### Example-1

> 子表达式的操作顺序其实没那么严格。[see](https://bugs.php.net/bug.php?id=61188)


```php
<?php
	function a() {echo 'a';}
	function b() {echo 'b';}
	a() == b(); // outputs "ab", 从左到右求值

	$a = 3;
	var_dump( $a == $a = 4 ); // outputs bool(true), 从右到左求值


```
#### Example-2

> PHP中的逗号运算符只有在for()条件中有用，在其他的表达式中无效。


```php
<?php
	// $a = 2, $b = 4; //无效
	// echo $a.$b."\n";

	for ($a = 2, $b = 4; $a < 3; $a++){
	   echo $a,$b."\n"; //outputs:2 4
	}


```