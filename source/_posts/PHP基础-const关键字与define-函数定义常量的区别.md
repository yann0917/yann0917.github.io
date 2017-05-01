---
title: PHP基础-const关键字与define()函数定义常量的区别
tags: php
categories: php
abbrlink: 13962
date: 2017-04-29 13:16:08
---


> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

常量是一个简单值的标识符（名字）。如同其名称所暗示的，在脚本执行期间该值不能改变（除了所谓的魔术常量，它们其实不是常量）。常量默认为大小写敏感。传统上常量标识符总是大写的。 

> 常量和变量有如下不同： 
>- 常量前面没有美元符号（$）；  
>- 在PHP5.3.0之前的版本中，常量只能用 define() 函数定义，而不能通过赋值语句；  
>- 常量可以不用理会变量的作用域而在任何地方定义和访问；  
>- 常量一旦定义就不能被重新定义或者取消定义；  
>- 常量的值只能是标量；
> 注意：在PHP5.6之后可以使用const关键字定义数组常量，在PHP7.0.0之后可以使用define()定义数组常量。 


### define()函数
```php
bool define(string $name, mixed $value[,bool $case_insensitive = false])
函数用于在运行时定义一个常量。
参数:
name：常量名。 
value：常量的值；
	仅允许标量和 null。标量的类型是 integer， float，string 或者 boolean。 
	也能够定义常量值的类型为 resource ，但并不推荐这么做，可能会导致未知状况的发生。
	PHP 7.0.0 版本后array values are allowed.  
case_insensitive：可选项，如果设置为 TRUE，该常量则大小写不敏感。默认是大小写敏感的。
返回值： 
	成功时返回 TRUE， 或者在失败时返回 FALSE。 

```
### const常量

> * PHP 5.3.0 后支持const关键字；
> * const 可以用在类内和类外；
> * PHP 7.1.0 之后可显式写访问控制修饰符，之前的版本均不可使用；
> * const定义的常量在PHP 5.6.0 之后可用于常量运算；
> * const的值除了标量外，PHP 5.6.0 版本后还可以是常量表达式，不能是变量，类属性，函数调用；

### Example-1


> 常量在PHP不同版本中的差异。


```php
<?php
	// PHP 5.3.0开始支持const关键字
	const CONSTANT = 'Hello World'; 
	echo CONSTANT;
	
	// PHP 5.6.0 const开始支持运算和数组常量
	const ANOTHER_CONST = CONSTANT.'; Goodbye World';
	echo ANOTHER_CONST;
	
	const ANIMALS = array('dog', 'cat', 'bird');
	echo ANIMALS[1]; // outputs "cat"
	
	// PHP 7.0.0 define()开始支持数组常量
	define('ANIMAL', array('dog','cat','bird'));
	echo ANIMAL[2]; // outputs "bird"

```

### Example-2
```php
<?php
	/**
	 * MyClass为了说明以下情况：
	 * 1.const可用于类内部
	 * 2.PHP7.1.0之后可显式写访问控制修饰符，之前的版本均不可使用
	 */
	class MyClass
	{
	    public const CONSTANT = 'constant value'; //PHP 7.1.0支持访问控制修饰符可见
	    //define('DEFINECONST','defineConst');    //语法错误，define()不能用于类内
	    function showConstant() {
	        echo  self::CONSTANT. "<br/>\n";
	    }
	}
	
	const ONE = 1; //const可在类外使用
	define('PI',3.14); //defnie()可在类外使用
	/**
	 * Foo类为了说明以下情况：
	 * 1.const定义的常量在PHP 5.6.0 之后可用于运算
	 * 2.const的值在PHP 5.6.0 之后可以是常量表达式
	 */
	class Foo {
	    // As of PHP 5.6.0
	    public $i=1;
	    const TWO = ONE * 2;
	    const THREE = ONE + self::TWO;
	    const SENTENCE = 'The value of THREE is '.self::THREE;
	    const CONST_VAR1 = 1 + 1; //支持常量算数运算
	    const CONST_VAR2 = 'Hello'.' World!' ; //支持字符串运算
	    const CONST_VAR3 = (1<<2); //支持常量位运算
	    const CONST_VAR4 = 1<0; //支持常量比较运算
	    const CONST_VAR5 = true||false; //支持常量逻辑运算
	    // const CONST_VAR6 = $i; //语法错误
	}
	echo Foo::SENTENCE ."<br/>\n";
	echo PI*2 ."<br/>\n"; //define()定义的常量可用于运算
	echo Foo::CONST_VAR1 ."<br/>\n";
	echo Foo::CONST_VAR2 ."<br/>\n";
	echo Foo::CONST_VAR3 ."<br/>\n";
	echo Foo::CONST_VAR4 ."<br/>\n";
	echo Foo::CONST_VAR5 ."<br/>\n";
	
	echo MyClass::CONSTANT . "<br/>\n";
	$class = new MyClass();
	$class->showConstant();
	
	define('DEFINE_VAR1',$class instanceof MyClass);
	// const CONST_VAR4 = $class instanceof MyClass; //const不支持类型运算符instanceof
	var_dump(DEFINE_VAR1);
	$i=4;
	define('DEFINE_VAR2', $i << 1);
	echo DEFINE_VAR2;
	
	echo $class::CONSTANT."<br/>\n"; // As of PHP 5.3.0

```
### Example-3

> 不能在函数内、循环内以及if语句之内用const来定义常量。

```php
<?php
	if (1) {
		// const CONST_VAR1 = 'BAR1'; //语法错误
		define('CONST_VAR2', 'BAR2');
	}

```
### Example-4


> define()可以定义大小写不敏感的常量，而const没有此功能。

```php
<?php
	//define()的第3个参数为true时，表示大小写不敏感
	define('DEFINE_VAR', 'define_test', true);
	echo DEFINE_VAR; //define_test
	echo define_var; //define_test
	echo DefINE_Var; //define_test
	
	const CONST_VAR = 'const_test';
	echo CONST_VAR;  //const_test
	echo const_var;  //PHP提示常量未定义
	echo ConST_Var;  //PHP提示常量未定义

```
### Example-5


> const定义的常量受命名空间的限制，define()定义的常量不受命名空间的限制。

```php
<?php
	namespace first;
	const CONST_VAR1 = 'FIRST_CONST_VAR1';
	define('DEFINE_VAR', 'define_var');

	namespace second;
	const CONST_VAR1 = 'SECOND_CONST_VAR1';
	define('DEFINE_VAR', 'define_var');//Notice: Constant DEFINE_VAR already defined 

```
### Example-6

> 使用define()定义常量时，可以使用PHP内置关键字声明常量，使用constant()返回常量值，但是使用const定义PHP内置关键字常量则会报错，不能重新声明常量。


```php
<?php
	define('TRUE','My constant value');
	echo constant('TRUE'); // outputs 'My constant value'

	//const ECHO = 'My constant value ECHO'; //致命错误，不能重新声明常量
	echo ECHO;
 
```


### Example-7 const关键词拓展

> php7.0.0之后self代表的类常量只能是当前类，PHP 7.0.0 之前的版本则有后期静态绑定的意味。


```php
<?php
	class A{ 
	   const X=1; 
	   const Y=self::X; 
	 } 
	 class B extends A{ 
	   const X=1.0; 
	 } 
	var_dump(B::Y); // PHP 5.6.29 运行结果为float(1), PHP 7.1.0 运行结果为int(1)

```
## Summary

PHP中const关键字和define()函数定义常量的区别：


### 版本差异


> 函数define()在PHP4和PHP5中均可使用，关键字const只能在PHP 5.3.0及其后的版本中使用。 
> PHP 5.6.0 const开始支持常量运算和数组常量
> PHP 7.0.0 define()开始支持数组常量


### 定义位置的区别

> 由于函数define()定义的常量是在 **`执行define()函数`** 时定义的，因此可以在函数内、循环内、if语句内等函数能够被调用的任何地方使用define()函数定义常量; 
> 由于const关键字定义的常量是在 **`编译`** 时定义的，因此const关键字定义常量必须处于最顶端的作用区域。这也就意味着不能在函数内、循环内以及if语句之内用const来定义常量。

### 对值的表达式支持的差异


> 虽然关键字const和define()定义的常量值都只能为null或标量数据(boolean，integer，float和string类型)以及resource类型(不推荐定义resource类型的常量，否则可能出现无法预知的结果)。PHP5.6.0 之前的版本中const关键字定义的常量值的表达式中不支持算术运算符、位运算符、比较运算符等多种运算符，而这些运算符在define()函数定义常量时都是可以直接使用的。
>
> php5.6.0 之后的版本中constant关键字的值支持常量表达式，表达式可以是算术运算符、位运算符、比较运算符等多种运算符，不过define()函数定义常量时还支持变量。
>
> define()支持instanceof操作符，const不支持instanceof操作符。

### 对命名空间限制的差异


> const定义的常量受命名空间的限制，define()定义的常量不受命名空间的限制。

### 对字符大小写敏感的支持差异


> 函数define()可以接收第3个参数，如果该参数为true，则表示常量名的大小写不敏感。而使用const关键字定义常量却没有提供类似的功能。

### 对系统内置关键字重新声明的差异

>  使用define()定义常量时，可以使用PHP内置关键字声明常量，使用constant()返回常量值，但是使用const定义PHP关键字常量则会报错，不能重新声明常量。


---


