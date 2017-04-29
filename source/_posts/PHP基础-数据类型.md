---
title: PHP基础-数据类型
date: 2017-04-28 22:14:36
tags: php
categories: php
---


> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

### PHP中的数据类型

> PHP 支持 8 种原始数据类型。 
> 
> - 四种标量类型： 
	 boolean（布尔型）  
	 integer（整型）  
	 float（浮点型，也称作 double)  
	 string（字符串）  
>
>
>- 两种复合类型： 
	 array（数组）  
	 object（对象）  
>
>
>- 最后是两种特殊类型： 
	 resource（资源）  
	 NULL（无类型） 

### Boolean
> 当转换为 boolean 时，以下值被认为是 FALSE： 
> - 布尔值 FALSE 本身  
> - 整型值0 （零）
> - 浮点型值 0.0（零）  
> - 空字符串，以及字符串 "0"  
> - 不包括任何元素的数组   
> - 特殊类型 NULL（包括尚未赋值的变量）  
> - 从空标记生成的 SimpleXML 对象  
>
> 所有其它值都被认为是 TRUE（包括任何资源）。 

#### Example-1
```php
<?php
	 $a= '0.00';
	 echo $a ?'true':'false';//true
?>
```
```php
<?php
	$array = array('key'=>true);
	if($array['key'] != 'false')
	     echo 'The value of key is '.$array['key'];
?>
```
```php
<?php
	var_dump(0 == 'all');//bool(true)
?>
```
```php
<?php 
	//TRUE和FALSE不能当成是1和0的常量
	$var1 = TRUE; 
	$var2 = FALSE; 

	echo $var1; // outputs: 1 
	echo $var2; //outputs: nothing
	echo (int)$var2; //outputs:0 

	//注意 true 和 false 输出结果的差异
	echo false ;		  //nothing
	echo (false) ;		//nothing
	echo false+false ;    //0
	echo (false+false) ;  //0
	echo intval(false) ;  //0
	echo '"'.false.'"' ;  //""
	echo "<hr/>";
	echo true ;  		//1
	echo (true) ;  	  //1
	echo true+true ; 	//2
	echo (true+true) ;   //2
	echo intval(true) ;  //1
	echo '"'.true.'"' ;  //"1"
?> 
```

### Integer
> 整型值可以使用十进制，十六进制，八进制或二进制表示，前面可以加上可选的符号（- 或者 +）。 
> 
> 二进制表达的 integer 自 PHP 5.4.0 起可用，PHP 5.4.0 之前的版本则报语法错误。
>
> 要使用八进制表达，数字前必须加上` 0 `（零）;要使用十六进制表达，数字前必须加上 `0x`;要使用二进制表达，数字前必须加上 `0b`。 
> 


#### Example-1
> - PHP 7.0.0 之前的版本，如果向八进制数传递了一个非法数字（即 8 或 9），则后面其余数字会被忽略；
> - PHP 7.0.0 之后，如果向八进制数传递了一个非法数字，则会解析错误；


```php
<?php
	var_dump(01090); // 八进制 010 = 十进制 8
?>
```
#### Example-2
> - 如果给定的一个数超出了 integer 的范围，将会被解释为 float。同样如果执行的运算结果超出了 integer 范围，也会返回 float；
> - PHP 不支持无符号整数；
> - 自 PHP 5.0.5 后，最大值可以用常量 `PHP_INT_MAX` 来表示；
> - 自 PHP 7.0.0 后，最小值可以用常量 `PHP_INT_MIN` 来表示；

```php
<?php
	$max = PHP_INT_MAX ; //PHP 5.0.5版本后支持该常量
	$min = PHP_INT_MIN; //PHP 7.0.0 版本之后支持该常量
	echo $max,$min;

	$large_number = 2147483647;
	var_dump($large_number);                     // int(2147483647)

	$large_number = 2147483648;
	var_dump($large_number);                     // float(2147483648)

	$million = 1000000;
	$large_number =  50000 * $million;
	var_dump($large_number);                     // float(50000000000)
?> 
```
#### Example-3

> 当从浮点数转换成整数时，将向下取整。如果浮点数超出了整数范围，则结果为未定义，因为没有足够的精度给出一个确切的整数结果。在此情况下没有警告，甚至没有任何通知！
> 
> 自 PHP7.0.0 之后 NAN和Infinity 强制转换成整型结果总是0  

```php
<?php
	var_dump((int)NAN); // PHP 7.0.0之前为 -2147483648，之后为0
	var_dump((int)INF); // PHP 7.0.0之前为 0 ，之后也是0
?> 
```
#### Example-4

> 决不要将未知的分数强制转换为 integer，这样有时会导致不可预料的结果。
 
```php
<?php
	echo (int) ((0.1+0.7) * 10); // outputs: 7
?> 
```
#### Example-5
> 数字前面有0代表八进制，但是字符串型数字前面有0并不是八进制。

```php
<?php
	$x = 0123;          // 83
	$y = "0123" + 0;    // 123 
	echo $x,$y;
?>
```
#### Example-6
>对超过int型最大值的数字进行取模运算时，会强制将float类型转化成int类型，从而出现错误。


```php
<?php
     $i = 6887129852; //超过int型最大值则为float类型
     echo "i=$i <br/>\n";
     echo "i%36=".($i%36)."<br/>\n";
     echo "alternative i%36=".($i-floor($i/36)*36)."\n";
?>
```
### Float


> 某些数学运算会产生一个由常量 NAN 所代表的结果。此结果代表着一个在浮点数运算中未定义或不可表述的值。任何拿此值与其它任何值进行的松散或严格比较的结果都是 FALSE。 
> 
> 由于 NAN 代表着任何不同值，不应拿 NAN 去和其它值进行比较，包括其自身，应该用 is_nan() 来检查。 
> 
> 浮点数之间不能进行比较，要测试浮点数是否相等，要使用一个仅比该数值大一点的最小误差值。


#### Example-1
```php
<?php
	echo intval(19.31*100),'<br/>'; //获取一个变量的整数,echo 1930
	echo number_format(19.31*100,20); //格式化数字
	echo "<hr/>";
	
	//不要拿浮点数进行比较
	$x = 8 - 6.4;  // which is equal to 1.6
	$y = 1.6;
	//echo number_format($y,20);
	var_dump($x == $y); // is not true
?>
```

### String
> 一个字符串 string 就是由一系列的字符组成，其中每个字符等同于一个字节。这意味着 PHP 只能支持 256 的字符集，因此不支持 Unicode 。
> 
> 一个字符串可以用 4 种方式表达： 
> - 单引号  
> - 双引号  
> - heredoc 语法结构  
> - nowdoc 语法结构（自 PHP 5.3.0 起） 


string 中的字符可以通过一个从 0 开始的下标，用类似 array 结构中的方括号包含对应的数字来访问和修改，比如 $str[42]，也可用花括号访问，比如 $str{42}。

PHP 的字符串在内部是字节组成的数组。因此用花括号访问或修改字符串对多字节字符集很不安全。



#### Example-1


> 自 PHP 5.4 起字符串下标必须为整数或可转换为整数的字符串，否则会发出警告。
>
> PHP 5.5 增加了直接在字符串原型中用` [] `访问字符的支持。

```php
<?php
	$str = 'abc';
	var_dump($str['1']);
	var_dump(isset($str['1']));
	echo "<br>";

	//以下字符串下标在 PHP 5.4 以上的版本中会发出警告。
	var_dump($str['1.0']);
	var_dump(isset($str['1.0']));
	echo "<br>";
	var_dump($str['x']);
	var_dump(isset($str['x']));
	echo "<br>";
	var_dump($str['1x']);
	var_dump(isset($str['1x']));

	//PHP 5.5 支持在字符串原型中使用[]访问字符
	var_dump('abc'[0]);
	var_dump('abc'{1});//在PHP 5.6 版本中解析错误，在PHP 7.1 上运行成功
?> 
```
#### Example-2


> 自 PHP 7.1.0 开始, 字符串支持负索引下标。


```php
<?php
	$array = [-3 => 'foo'];
	echo "The element at index -3 is $array[-3].";
?> 
```
#### Example-3
> 双引号内可以解析单引号内的变量，但是在关联数组中，双引号并不能解析单引号内的索引。
> 解决办法是使用复杂语法{}或者去掉索引的单引号。

```php
<?php
	$str = 'abc';
	echo "this is a '$str'".'<br/>';

	$fruit=array('a'=>'apple','b'=>'banana');
	echo "This is a $fruit['a']";  //语法错误T_ENCAPSED_AND_WHITESPACE
	//解决办法
	echo "This is a $fruit[a]";
	echo "This is a {$fruit['a']}";
	echo "This is a ${fruit['a']}";
?>
```

### Array

> PHP 中的数组实际上是一个`有序映射`。映射是一种把 values 关联到 keys 的类型。此类型在很多方面做了优化，因此可以把它当成真正的数组，或列表（向量），散列表（是映射的一种实现），字典，集合，栈，队列以及更多可能性。由于数组元素的值也可以是另一个数组，树形结构和多维数组也是允许的。 
> 
> key 可以是 integer 或者 string。value 可以是任意类型。 
> 
> 如果对给出的值没有指定键名，则取当前最大的整数索引值，而新的键名将是该值加一。如果指定的键名已经有了值，则该值会被覆盖。 


#### Example-1

> 只对某些单元指定键名而对其它的空置，新的键名将是该值+1，如果当前还没有整数索引，则键名将为 0.


```php
<?php
$array = array(
         "a",
         "b",
    6 => "c",
         "d",
);
var_dump($array);
?> 
```

#### Example-2
> 自 5.4 起可以使用短数组定义语法，用 [] 替代 array()。

```php
<?php
$array1 = array(
    "foo" => "bar",
    "bar" => "foo",
);
// 自 PHP 5.4 起，可使用[]代替array()
$array2 = [
    "foo" => "bar",
    "bar" => "foo",
];
?> 
```
#### Example-3
> 如果在数组定义中多个单元都使用了同一个键名，则只使用了最后一个，之前的都被覆盖了。

```php
<?php
	$array = array(
	    1    => "a",
	    "1"  => "b",
	    1.5  => "c",
	    true => "d",
	);
	var_dump($array);
?> 
```
```php
<?php
$array = array(0,
     1,
     "2.99999999" => 56,
     2 => 2,
     3.9999 => 3,
     3 => 3.1,
     1 => 4,
     0 => 6,
 );
print_r($array);
?> 
```

#### Examlpe-4


> 自 PHP 5.4 起可以用数组间接引用函数或方法调用的结果。之前只能通过一个临时变量。 
> 
> 自 PHP 5.5 起可以用数组间接引用一个数组原型。 

```php
<?php
	function getArray() {
	    return array(1, 2, 3);
	}
	$secondElement = getArray()[1];// on PHP 5.4

	// PHP 5.4 之前只能通过一个临时变量
	$tmp = getArray();
	$secondElement = $tmp[1];
	// or
	list(, $secondElement) = getArray();
?>
```
#### Example-5
> unset() 函数允许删除数组中的某个键。但要注意数组并不会重建索引。如果需要删除后重建索引，可以用 array_values() 函数。

```php
<?php
	// 创建一个简单的数组
	$array = array(1, 2, 3, 4, 5);
	print_r($array);
	
	// 现在删除其中的所有元素，但保持数组本身不变:
	foreach ($array as $i => $value) {
	    unset($array[$i]);
	}
	print_r($array);
	
	// 添加一个单元（注意新的键名是 5）
	$array[] = 6;
	print_r($array);
	
	// 重新索引：
	$array = array_values($array);
	$array[] = 7;
	print_r($array);
?>
```
#### Example-6

> 在双引号字符串中，不给索引加上引号是有效的，但是最好始终在用字符串表示的数组索引上加上引号。
> 以下是例子错的，但可以正常运行。错误是因为代码中有一个未定义的常量（bar）而不是字符串（'bar'－注意引号），而 PHP 可能会在以后定义此常量，不幸的是你的代码中有同样的名字。它能运行，是因为 PHP 自动将裸字符串转换成一个其值为该裸字符串的正常字符串。


```php
<?php
	$foo[bar] = 'enemy';
	echo $foo[bar];
?>
```

#### Example-7
> 对于任意 integer，float，string，boolean 和 resource 类型，如果将一个值转换为数组，将得到一个仅有一个元素的数组，其下标为 0，该元素即为此标量的值。
>
> 如果一个 object 类型转换为 array，则结果为一个数组，其单元为该对象的属性。键名将为成员变量名，不过有几点例外：静态属性不可访问；私有变量前会加上类名作前缀；保护变量前会加上一个 '*' 做前缀。这些前缀的前后都各有一个 NULL 字符(\0)。


```php
<?php
	class A {
	    private $A; // 转换成数组后索引为\0A\0A
	}
	
	class B extends A {
	    private $A; // 转换成数组后索引为\0B\0A
	    public $AA; // 转换成数组后索引为AA
	}
	
	var_dump((array) new B());
?> 
```


### Object

> 如果将一个对象转换成对象，它将不会有任何变化。如果其它任何类型的值被转换成对象，将会创建一个内置类` stdClass `的实例。如果该值为 NULL，则新的实例为空。数组转换成对象将使键名成为属性名并具有相对应的值。对于任何其它的值，名为 `scalar` 的成员变量将包含该值。 

#### Example-1
```php
<?php
	$obj1 = (object) array('1' => 'foo', 2=>'bar');
	var_dump($obj1);  
	// outputs : object(stdClass)#1 (2) { [1]=> string(3) "foo" [2]=> string(3) "bar" }
	var_dump(isset($obj1->{'1'})); 
	// outputs : 'bool(false)'
	var_dump(key($obj1)); 
	// outputs : 'int(1)'

	$obj2 = (object) 'ciao';
	var_dump($obj2);
	echo $obj2->scalar;  // outputs : 'ciao'
?> 
```
#### Example-2
> 自 5.4 起可以使用短数组定义语法，创建一个stdClass对象。


```php
<?php
   $object =(object) [
     'propertyOne' => 'foo',
     'propertyTwo' => 42,
   ];
   var_dump($object);
?> 
```
#### Example-3
> 自 PHP 7.0.0 之后，多了以下几种创建空对象的方法。（在PHP 7.1.0上成功运行）


```php
<?php
	$obj1 = new \stdClass; // stdClass 对象实例
	$obj2 = new class{}; // class@anonymous 对象实例
	$obj3 = (object)[]; // 空数组强转对象
	echo "<pre>";
	var_dump($obj1); // object(stdClass)#1 (0) {}
	var_dump($obj2); // object(class@anonymous)#2 (0) {}
	var_dump($obj3); // object(stdClass)#3 (0) {}
?>
```

### Resource


> 资源 resource 是一种特殊变量，保存了到外部资源的一个引用。资源是通过专门的函数来建立和使用的。
>
> 由于资源类型变量保存有为打开文件、数据库连接、图形画布区域等的特殊句柄，因此将其它类型的值转换为资源没有意义。
>
> 由于 Zend 引擎引进了引用计数系统，可以自动检测到一个资源不再被引用了（和 Java 一样）。这种情况下此资源使用的所有外部资源都会被垃圾回收系统释放。因此，很少需要手工释放内存。 请注意：持久数据库连接比较特殊，它们不会被垃圾回收系统销毁。

### NULL
> NULL 值表示一个变量没有值。NULL 类型唯一可能的值就是 NULL。 
> 
> 使用 (unset) $var 将一个变量转换为 null 将不会删除该变量或 unset 其值。仅是返回 NULL 值而已。

#### Example-1
```php
<?php
	$test['_string'] = "";
	$test['_int'] = 0;
	$test['_float'] = 0.0;
	$test['_null'] = null;
	$test['_arr'] = array();
	$test['_obj'] = new \stdClass; //is not null

	 foreach($test as $key => $value)
	 {
	     echo $key.": ";
	     if($value == null)
	         echo "is null\n";
	     else
	         echo "is not null\n";
	 }
	 die();
?>
```
#### Example-2

> 有趣的是貌似只有当变量`$a="0"`的时候以下条件才能成立。

```php
<?php
	$a = '0';
	echo ($a != NULL) && ((bool)$a == NULL) ?'not null':'null';
?>
```
#### Example-3
> 注意unset($a)函数销毁变量和$a = NULL的区别


```php
<?php
	// unset($a)
	$a = 5;
	$b = & $a;
	unset($a);
	print "b $b "; // b 5 

	// $a = NULL; 
	$a = 5;
	$b = & $a;
	$a = NULL;
	print "b $b "; // b 
	print(! isset($b)); // 1 
?> 
```
----------
