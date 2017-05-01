---
title: PHP基础-运算符
tags: php
categories: php
abbrlink: 6988
date: 2017-04-29 13:21:06
---


> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

### 运算符

运算符是可以通过给出的一或多个值（表达式）来产生另一个值（因而整个结构成为一个表达式）的东西。

> PHP中运算符包括如下内容：
> 
> - 运算符优先级
> - 算术运算符
> - 赋值运算符
> - 位运算符
> - 比较运算符
> - 错误控制运算符
> - 执行运算符
> - 递增／递减运算符
> - 逻辑运算符
> - 字符串运算符
> - 数组运算符
> - 类型运算符


### 运算符优先级

PHP中运算符优先级请参考：[运算符优先级](http://php.net/manual/zh/language.operators.precedence.php)

#### Example-1


> 注意：逻辑运算符的两套符号优先级不同，优先级从高到低依次为：`&& > || > = > and > xor > or`


```php
<?php
	$bool = true && false;
	var_dump($bool); // false

	$bool = true and false;
	var_dump($bool); // true

```
### 算术运算符

> - 除法运算符总是返回浮点数。只有两个操作数都是整数（或字符串转换成的整数）并且正好能整除时返回一个整数；
> 
> - 取模运算符的操作数在运算之前都会转换成整数（向下取整，除去小数部分）；
> 
> - 取模运算符 % 的结果和被除数的符号相同，即` $a % $b `的结果和 $a 的符号相同；

#### Example-1

> PHP 5.6 新增幂运算符，等同于pow()函数;

```php
<?php
   $a=3;
   $b=4;
   echo $a**$b; //PHP 5.6 新增幂运算符

```
#### Example-2

> 取模运算符`%`只在integer数据范围内有效，对较大的数进行取模运算的时候最好使用`fmod()`函数代替取模运算符`%`；

```php
<?php
    echo 3333333333 % 3,'<br/>'; //prints -1
    echo fmod(3333333333 ,3); //prints 0
 
```

### 赋值运算符
#### Example-1

> PHP先计算组合运算符右侧的值，最后再用左侧的变量和右侧表达式的结果进行计算；
> 和javascript计算结果不同。

```php
<?php
	$a = 0;
	$a -= (++$a) + (++$a);
	echo $a; //outputs: -1,右侧计算结果为3,此时$a为2，最后结果是2-3=-1
?>
```
```javascript
<script>
	var a = 0;
	a -= (++a) + (++a);
	alert(a); //alert: -3，等效于a=a-((++a)+(++a));
</script>
```

### 位运算符
> 位移在 PHP 中是数学运算。向任何方向移出去的位都被**丢弃**。左移时右侧以零填充，符号位被移走意味着正负号不被保留。右移时左侧以符号位填充，意味着正负号被保留。 
> 
> 如果位运算符`&,|,^`两侧都是string类型的，则按照ASCII码值进行计算，返回的结果也是string类型，其他情况下则会强制转化成整型，结果必然也是整型。
> 
> 如果位运算符 `~` 是string类型的，则按照ASCII码值进行计算，返回的结果也是string类型，其他情况下则会强制转化成整型，结果必然也是整型。
> 
> 位运算符`<<,>>`总是被转换成整型计算。

#### Example-1

> 字符串类型的位运算


```php
<?php
if (('18' & '32') == '10') {
	//ord()返回字符串第一个字符的ASCII
    echo ord('18'); //return 十进制 49, 数字1的二进制为 110001
    echo ord('32'); //return 十进制 51, 数字3的二进制为 110011
    echo ord('10'); //return 十进制 49, 数字1的二进制为 110001
    //因此 110001 & 110011 = 110001
}

```

### 比较运算符

如果比较一个数字和字符串或者比较涉及到数字内容的字符串，则字符串会被转换为数值并且比较按照数值来进行。

#### Example-1

PHP 7.0 新增以下两个比较运算符：
> - 结合比较运算符 `$a<=>$b` ，当$a小于、等于、大于 $b时，分别返回一个小于、等于、大于0的integer 值；
> - NULL 合并操作符 `??` ，返回从左往右第一个存在且不为 NULL 的操作数，如果都没有定义且不为 NULL，则返回 NULL。

```php
<?php
	$foo = null;
	$bar = null;
	$baz = 1;
	$qux = 2;
	echo $foo ?? $bar ?? $baz ?? $qux; // outputs 1
?> 
```
#### Example-2

> 三目运算符可以省略第二个表达式。表达式` expr1 ?: expr3 `在 expr1 求值为 TRUE 时返回 expr1，否则返回 expr3，同时报 `ERR_NOTICE`错误。
> 
> 比较常用的写法是`(isset($var) && $var) ? $var : 'default_value'`，如果忽略Notice错误，则可简写成 `@($var?:'default_value')`;


```php
<?php
	$a = array();
	$a['foo'] = 'oof';

	$b = @($a['foo'] ?: 'No foo');
	$c = @($a['bar'] ?: 'No bar');

	echo $b.PHP_EOL.$c; //outputs: oof No bar


```
#### Example-3

> 三目运算符表达式和字符串拼接的时候请小心，因为运算符优先级的问题，将会产生意想不到的结果，建议将表达式用圆括号`()`括起来。


```php
<?php
	echo "a string that has a " . (true) ? 'true' : 'false' . "condition in.";
	//echo true

```
#### Example-4
> 建议使用`!$var=='str'`代替`$var != 'str'`做判断，在PHP 7.1.0上测试运行时间如下：


```php
<?php 
for($bench = 0; $bench < 3; $bench++) { 
    $start = microtime(true); 
    $a = 1; 
    $b = 0;
    for($i = 0; $i < 100000000; $i++) { 
    	if(!$a == 'hello') {
    		$b++; 
    	}
    } 
    $end = microtime(true); 
    echo "Used time: " . ($end-$start) . "<br/>\n"; 
 } 
 /**
  * $a != 'hello'
  * Used time: 8.529669046402
  * Used time: 8.1041309833527
  * Used time: 8.2515349388123
  * 
  * !$a == 'hello'
  * Used time: 5.8349351882935
  * Used time: 5.6691370010376
  * Used time: 5.6830477714539
  */

```
#### Example-5
> 当使用比较运算符（==）比较两个对象变量时，比较的原则是：如果两个对象的**属性**和**属性值**都相等，而且两个对象是同一个类的实例，那么这两个对象变量相等。
>
> 而如果使用全等运算符（===），这两个对象变量一定要指向某个类的同一个实例（即同一个对象）。

```php
<?PHP
	class A { 
	     private $value; 
	     function __construct ($value) { 
	         $this->value = $value; 
	     } 
	 } 
	 class B { 
	     private $value; 
	     function __construct ($value) { 
	         $this->value = $value; 
	     } 
	 } 
	
	$a1 = new A (1); 
	$a2 = new A (2); 
	$b1 = new B (1); 
	
	var_dump( $a1 == $a2 );  //bool(false)
	var_dump( $a1 == $b1 );  //bool(false)

```

#### Example-6

> 数组与数组的比较，具有较少成员的数组较小，如果运算数 1 中的键不存在于运算数 2 中则数组无法比较，否则挨个值比较。


```php
<?php
	$var_a = array(1 => 1, 2 => 0, 3 => 1);
	$var_b = array(1 => 1, 3 => 0, 2 => 1);
	if(1){ //此处代码无法解释
		//($var_a > $var_b，$var_a < $var_b，$var_a != $var_b)均为true
		var_dump($var_a > $var_b);  // bool(true)
		var_dump($var_b > $var_a);  // bool(true)
		var_dump($var_b == $var_a);  // bool(false)
		echo "<br/>";
		//($var_c > $var_d ,$var_c < $var_d ,$var_c==$var_d)均为false
		$var_c = array(1 => 1, 2 => 1, 3 => 0);
		$var_d = array(1 => 1, 3 => 1, 2 => 0);

		var_dump($var_c > $var_d); // bool(false)
		var_dump($var_d > $var_c); // bool(false)
		var_dump($var_d == $var_c); // bool(false)
		echo "<br/>";
	}
	$var_e = array(1 => 1, 2 => 0, 3 => 1);
	$var_f = array(1 => 1, 2 => 1, 3 => 0);

	var_dump($var_e > $var_f);  // bool(false)
	var_dump($var_f > $var_e);  // bool(true)
	var_dump($var_e == $var_f);  // bool(false)
	echo "<br/>";
 
```

### 错误控制运算符
> 当将` @ `放置在一个 PHP 表达式之前，该表达式可能产生的任何错误信息都被忽略掉。
> 
> 运算符只对**表达式**有效。简单的规则就是：如果能从某处得到值，就能在它前面加上 @ 运算符。


#### Example-1
> 谨慎使用 `@` ，它会降低代码执行速度，它是代码执行速度与便捷的妥协，代码在PHP 5.3.29 版本上测试结果如下：

```php
<?php 
	$start =microtime(true);
	function x() { } 
	for ($i = 0; $i < 1000000; $i++) { @x(); } 
	$end = microtime(true);
	echo "Used time: " . ($end-$start) . "<br/>\n";
	/**
	 * without @
	 * Used time: 0.16029000282288
	 * with @
	 * Used time: 0.7277979850769
	 */

```


### 执行运算符
> PHP 支持一个执行运算符：反引号（``），PHP 将尝试将反引号中的内容作为 shell 命令来执行，并将其输出信息返回。
> 
> 反引号不能在双引号字符串中使用。

#### Example-1

```php
<?php
    header('content-type:text/html;charset=gbk');
    $host = 'www.baidu.com';
    echo `ping -n 3 {$host} \n`;

```

### 递增／递减运算符

> 字符变量的递增/递减运算时，字符变量只能递增，不能递减，并且只支持纯字母（a-z 和 A-Z）。递增／递减其他字符变量则无效，原字符串没有变化。

#### Example-1
> 字符变量只能递增，不能递减。


```php
<?php
	$s = 'W';
	for ($n=0; $n<10; $n++) {
	    echo ++$s . ' '; //X Y Z AA AB AC AD AE AF AG 
	}
	echo "<br/>";
	for ($n=10; $n>0; $n--) {
	    echo (--$s) . ' ';//AG AG AG AG AG AG AG AG AG AG
	}
?>
```
#### Example-2

> 字符串里有字符'E'且后面紧跟着数字，则会被当成科学计数法，此时字符串的递增、递减运算会转换成数字运算。


```php
<?php
	$a="9D9"; var_dump(++$a);  // string(3) "9E0"
	$a="9E2"; var_dump(++$a);   // float(901)

```

### 逻辑运算符

> "与"和"或"有两种不同形式运算符(` && / and `与` || / or `)的原因是它们运算的优先级不同，如果不记得优先级，建议给表达式加上圆括号`()`，防止出现意想不到的结果。

#### Example-1

```php
<?php
	$e = false || true;
	$f = false or true;// false 被赋给 $f，true 被忽略，等价于(($f = false) or true)
	
	var_dump($e, $f);

```

### 字符串运算符


### 数组运算符
> 数组运算符包括联合、全等、相等、不全等、不等运算操作。


#### Example-1

> ` + `运算符把右边的数组元素附加到左边的数组后面，如果两个数组中有相同的键名，则只用左边数组中的，右边的被忽略。


```php
<?php
	$a = array("a" => "apple", "b" => "banana");
	$b = array("a" => "pear", "b" => "strawberry", "c" => "cherry");
	
	echo "<pre>";
	$c = $a + $b; // Union of $a and $b
	var_dump($c);
	
	$c = $b + $a; // Union of $b and $a
	var_dump($c);
	
	$a += $b; // Union of $a += $b is $a and $b
	var_dump($a);

```
#### Example-2

> 再次强调，数组中的 `+` 是联合的意思，并不是将数组中的每一个元素相加。


```php
<?php
	$a = array(1,2,3);
	$b = array(4,5,6,7,8);
	$c = $a + $b; // Union of $a and $b
	print_r($c); //Array ([0] => 1 [1] => 2 [2] => 3 [3] => 7 [4] => 8)
 
```

### 类型运算符 

> `instanceof` 用于确定一个对象是否属于某一**类**的实例；
> 
> `instanceof` 也可用来确定一个对象是不是继承自某一**父类的子类**的实例；
> 
> `instanceof` 也可用于确定一个对象是不是实现了某个**接口对象**的实例；
> 

#### Example-1

```php
<?php
	class MyClass1{
	}
	interface MyInterface{
	}
	class MyClass2 implements MyInterface{
	}

	$a = new MyClass1;
	$b = new MyClass2;
	var_dump(!$a instanceof stdClass); //bool(true)
	var_dump($b instanceof MyClass2); //bool(true)
	var_dump($b instanceof MyInterface); //bool(true)


```

---
