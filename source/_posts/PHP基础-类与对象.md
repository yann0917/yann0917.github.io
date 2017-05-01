---
title: PHP基础-类与对象
tags: php
categories: php
abbrlink: 43505
date: 2017-04-29 13:42:58
---


> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

PHP 对待对象的方式与引用和句柄相同，即每个变量都持有对象的引用，而不是整个对象的拷贝。

### 类常量
#### Example-1

> 自 PHP 5.5 起，关键词 `class` 也可用于类名的解析。使用 ClassName::class 可以获取一个字符串，包含了类 ClassName 的完全限定名称，这对使用了 **命名空间** 的类尤其有用。


```php
<?php
    namespace NS {
        class ClassName {
        }
    
    echo ClassName::class; //outputs NS\ClassName
}


```
#### Example-2

> 属性和方法位于不同的“存储空间”，这就意味着不能直接调用一个赋值给属性的匿名方法，需要先把属性赋值给一个变量。
> 
> PHP 7.0.0 之后则可以将该属性用 `()` 括起来然后直接调用该匿名方法。

```php
<?php	
    class Foo{
        public $bar;
	    
        public function __construct() {
            $this->bar = function() {
            return 2;
            };
        }
    }

    $obj = new Foo();

    // as of PHP 5.3.0:
    $func = $obj->bar;
    echo $func(), PHP_EOL;
	
    // alternatively, as of PHP 7.0.0:
    echo ($obj->bar)(), PHP_EOL; 


```
#### Example-3

> PHP 5.4 引进了一种新的创建对象后调用成员方法的表达式。

```php
<?php

    echo (new DateTime())->format('Y');


```
### 属性
#### Example-1

> 属性常量的初始化值必须是常量，PHP 5.6 之后也可以是常量表达式。

```php
<?php
class SimpleClass{
   //PHP 5.6.0+有效:
   public $var1 = 'hello ' . 'world';
   //PHP 5.3.0+有效:
   public $var2 = <<<EOD
hello world
EOD;
   //PHP 5.6.0+有效:
   public $var3 = 1+2;
   // 无效的属性声明:
   // public $var4 = self::myStaticMethod();
   // public $var5 = $myVar;

   // 有效的属性声明:
   public $var6 = myConstant;
   public $var7 = array(true, false);

   //PHP 5.3.0+有效:
   public $var8 = <<<'EOD'
hello world
EOD;
}


```
#### Example-2

> `$this` 可以强转成数组。如果一个 object 类型转换为 array，则结果为一个数组，其单元为该对象的属性。键名将为成员变量名，不过有几点例外：静态属性不可访问；私有变量前会加上类名作前缀；受保护变量前会加上一个 '*' 做前缀。这些前缀的前后都各有一个 NULL 字符(\0)。


```php
<?php 

    class TestA { 
        public $var1 = 1; 
        protected $var2 = 2; 
        private $var3 = 3; 
        static $var4 = 4; 
	     
        public function toArray() { 
            return (array) $this; 
        } 
    } 
    class TestB extends TestA{
        public $var5 = 5;
        protected $var6 = 6;
        private $var7 = 7;
        static $var8 = 8;
    }

    $t = new TestB; 
    print_r($t->toArray()); 


```
### 构造方法

与其它方法不同，当` __construct() `被与父类` __construct()` 具有不同参数的方法覆盖时，PHP 不会产生一个 E_STRICT 错误信息。即构造方法可以重载。

#### Example-1

> 尝试从析构方法中抛出异常会导致fatal error，然而以下代码在PHP 7.1.0上会导致出错，在PHP 5.3.29 和PHP 5.6.29都能正常运行。


```php
<?php
class Something {

     public function __construct() {
         echo __METHOD__ . '<br/>';
     }

     public function __destruct(){
         echo __METHOD__ .'<br/>';
         throw new Exception("Exception thrown out of ::__destruct()", 1);
     }
 }
 try{
     $Something = new Something();
     $Something = null; // will cause to call the objects ::__destruct()
     // also possible: unset($Something);
}catch(Exception $e){
     echo 'Exception: ' . $e->getMessage() . PHP_EOL.'<br/>';
 }
 echo 'End of script -- no Fatal Error.';


```
### 访问控制

> 对属性或方法的访问控制，是通过在前面添加关键字 public（公有），protected（受保护）或 private（私有）来实现的。
> 
> - 被定义为公有的类成员可以在任何地方被访问。
> 
> - 被定义为受保护的类成员则可以被其自身以及其子类和父类访问。
> 
> - 被定义为私有的类成员则只能被其定义所在的类访问。

#### Example-1

> 上述说法并不严谨，通过`call_user_func()`和`Closure::bind()`复制一个指定的绑定对象和类作用域的闭包，可以在类外访问类成员内部的私有成员和受保护成员。


```php
<?php
    class Sealed { 
        private $value = 'foo'; 
    }
	
    $sealed = new Sealed;
    var_dump($sealed); // private $value => string(3) "foo"
	
    // call_user_func — 把第一个参数作为回调函数调用。
    // PHP5.4 Closure::bind — 复制一个闭包，绑定指定的$this对象和类作用域。
    call_user_func(\Closure::bind(
        function () use ($sealed) { $sealed->value = 'BAZ'; },
        null,
        $sealed
    ));
	
    var_dump($sealed); // private $value => string(3) "BAZ"


```
#### Example-2

> PHP 5.3 之后可以使用简单的变量动态调用类的静态方法或常量，但是使用对象属性调用类的静态方法或类常量，在PHP 7.0之前的版本会抛出语法错误。


```php
<?php
    class Foo{
        const BAR = 'const';
	     
        public static function bar(){
            return 'static';
        }
    }
	
    echo Foo::BAR ; // outputs "const"
    echo Foo::bar() ; // outputs "static"
	
    $var = 'Foo';
    echo $var::BAR ; // outputs "const" in PHP 5.3.0
    echo $var::bar(); // outputs "static" in PHP 5.3.0
	
    //PHP 7.0 之后有效
    $object = new stdClass; 
    $object->class = 'Foo'; 
    echo $object->class::BAR . PHP_EOL; // outputs "const" in PHP 7
    echo $object->class::bar() . PHP_EOL; // outputs "static" in PHP 7


```
### 抽象类

#### Example-1

> 不能在抽象类的内部通过self实现抽象类内部的抽象方法。


```php
<?php
    abstract class Basic {
        public static function doWork() {
        return (new self())->work();
    }

    abstract protected function work();
    }

    class Keeks extends Basic {
        protected function work() {
        return 'Keeks';
    }
    }

    echo Keeks::doWork();


```
#### Example-2

> 继承一个抽象类的时候，子类必须定义父类中的所有抽象方法；另外，这些方法的访问控制必须和父类中一样（或者更为宽松）。
> 此外抽象方法的调用方式必须匹配，即类型和所需参数数量必须一致。（如果子类参数列表中有默认参数则例外）

```php
<?php
    // fatal error,子类方法的调用方法必须和抽象父类的类型和数量一致
    abstract class my_class {
        abstract public function my_function($number);
    }

    class subclass extends my_class {
        //now $string isn't initialized
        public function my_function($new_number, $string ){
            echo $new_number . $string;
        }
    }

    $var = new subclass();
    $var->my_function(1024, ' is an integer!!!');


```

> 子类可以定义父类抽象方法中不存在的可选参数。

```php
<?php
    // 子类的方法调用必须和抽象类的类型和参数一致
    // 但是子类定义的方法参数和数量可以和父类的不一致（如子类设置了默认参数）
    abstract class my_class {
        abstract public function my_function($number);
    }

    class subclass extends my_class {
        public function my_function($new_number, $string = ' is an integer!!!'){
            echo $new_number . $string;
        }
    }
    $var = new subclass();
    $var->my_function(1024); 


```
> 子类的方法调用可以和抽象父类的类型和参数不一致，如重写了子类的默认参数。

```php
<?php
    // 子类的方法调用可以和抽象父类的类型和参数不一致
    // 子类定义的方法参数和数量可以和父类的不一致（如重写了子类的默认参数）
    abstract class my_class {
        abstract public function my_function($number);
    }
    
    class subclass extends my_class {
        public function my_function($new_number, $string = ' is a float!!!'){
            echo $new_number . $string;
        }	
    }
    $var = new subclass();
    //added ' is an integer'
    $var->my_function(1024, ' is an integer!!!'); 


```
#### Example-3

> 抽象类的抽象方法可以是静态的，但最好不要这么做。虽然在PHP 7.1上无错误，但是在PHP5.6.29上提示Strict standards：静态方法不能用在抽象类里。这是因为静态方法只能由该类调用，但是抽象类意味着一定要有其他的类去实现它的方法。
> 
> 虽然抽象类不能被实例化，但是抽象类可以调用类内的静态非抽象方法。

```php
<?php

    abstract class FOO{
        abstract static function dump();

        static function bar(){
            echo "test\n";
        }
    }

    class BAR extends FOO{
        static function dump(){var_dump("BAR");}
    }
    BAR::dump(); // string(3) "BAR"
    FOO::bar(); // test


```
#### Example-4

> 抽象类不一定是基类，也可以继承自非抽象类。

```php

<?php
    class Foo {
        public function sneeze() { echo 'achoooo','<br/>'; }
    }

    abstract class Bar extends Foo {
        public abstract function hiccup();
    }

    class Baz extends Bar {
        public function hiccup() { echo 'hiccup!','<br/>'; }
    }

    $baz = new Baz();
    $baz->sneeze(); // achoooo
    $baz->hiccup(); //hiccup!


```
### 对象接口

- 要实现一个接口，使用` implements `操作符。类中必须实现接口中定义的所有方法，否则会报一个致命错误。
- 类可以实现多个接口，用逗号来分隔多个接口的名称。 接口也可以多继承，通过使用` extends` 操作符。
- 实现多个接口时，接口中的方法不能有重名。
- 类要实现接口，必须使用和接口中所定义的方法完全一致的方式，否则会导致致命错误。
- 接口中也可以定义常量。接口常量和类常量的使用完全相同，但是不能被子类或子接口所重写。

#### Example-1

> 实现接口的方法时，如果方法的参数中设置了默认值，则可以和接口中定义的方法不一致，且不会报错。

```php
<?php

    interface Foo {
        public function foo($foo);
    }

    class Bar implements Foo {
        public function foo($foo, $bar = null) {
            return $foo . $bar;
        }
    }

    $bar = new Bar();
    echo $bar->foo('Study', 'Good'); 


```
#### Example-2

> 接口中的常量不能被子类或子接口重写，但是可以使用一个(抽象)类实现该接口，然后另一个类继承实现该接口的类，就可以重写接口常量。

```php
<?php
    interface inter1 {
        const interface1 = "I am from interface 1";
    }

    abstract class AbsClass implements inter1 {
	     
    }

    class Test extends AbsClass {
        const interface1 = "I am from test class";

        public function display() {
            echo inter1::interface1;
            echo "<br/>";
            echo Test::interface1;
        }
    }

    $Obj = new Test();
    $Obj->display();


```
#### Example-3

> 可以在类声明之前实例化该类的对象，但是并不能在实现接口之前实例化实现接口的类。

```php
<?php
    $bar = new FooA(); // 有效
    class FooA{}

    // $bar = new FooB(); // 无效 - 抛出致命错误，找不到类FooB
    interface foo2{}
    class FooB implements foo2{}
    $bar = new FooB();


```

### Trait

自 PHP 5.4.0 起，PHP 实现了一种代码复用的方法，称为 trait。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。[详见手册](http://php.net/manual/zh/language.oop5.traits.php)

#### Example-1

> 从基类继承的成员会被 trait 插入的成员所覆盖。优先顺序是来自当前类的成员覆盖了 trait 的方法，而 trait 则覆盖了被继承的方法。

```php
<?php
    trait Hello {
        public function sayHello() {
            echo 'Hello ';
        }
    }
	
    trait World {
        public function sayWorld() {
            echo 'World';
        }
    }
	
    class MyHelloWorld {
        use Hello, World; // 通过逗号分隔，在 use 声明列出多个 trait
        public function sayExclamationMark() {
            echo '!';
        }
    }
	
    $o = new MyHelloWorld();
    $o->sayHello();
    $o->sayWorld();
    $o->sayExclamationMark();


```
#### Example-2

> 如果 trait 定义了一个属性，那类将不能定义同样名称的属性，否则会产生一个错误。如果该属性在类中的定义与在 trait 中的定义兼容（同样的可见性和初始值）则错误的级别是 E_STRICT，否则是一个致命错误。 

```php
<?php
    trait PropertiesTrait {
        public $same = true;
        public $different = false;
    }
	
    class PropertiesExample {
        use PropertiesTrait;
        public $same = true; // Strict Standards
        public $different = true; // 致命错误
    }


```

### 匿名类
#### Example-1

> PHP 7 开始支持匿名类。可以传递参数到匿名类的构造器，也可以继承（extends）其他类、实现接口（implement s），以及像其他普通的类一样使用 trait。

```php
<?php

    class SomeClass {}
    interface SomeInterface {}
    trait SomeTrait {}

    var_dump(new class(10) extends SomeClass implements SomeInterface {
        private $num;

        public function __construct($num){
            $this->num = $num;
        }

        use SomeTrait;
    }); 


```
#### Example-2
> 匿名类由引擎分配名字，如下所示：

```php
<?php

    echo get_class(new class {}); // class@anonymous G:\www\php\\test.php00480021


```

### 重载

PHP所提供的"重载"（overloading）是指动态地"创建"类属性和方法。我们是通过魔术方法（magic methods）来实现的。 
当调用当前环境下未定义或不可见的类属性或方法时，重载方法会被调用。
所有的重载方法都必须被声明为 public。 

#### Example-1

> 程序运行时已经存在类的实例化对象的属性，但是被unset()之后，也可以实现重载。

```php
<?php
    class Test { 

        public $property1;

        public function __get($name) {
            return "Get called for " . get_class($this) . "->\$$name \n";
        }

    }

    $Test = new Test();
    unset($Test->property1); // enable overloading
    echo $Test->property1; // Get called for Test->$property1


```
#### Example-2

> 可以通过魔术方法在类外获取私有属性。但是不应该在程序运行时动态改变属性值。

```php
<?php
    class order{
        private $OrderID='';
        private $OrderAmount=0.00;
	     
        public function __set($name='', $value=''){
            if(property_exists($this, $name)){
                $this->$name = $value;
            }
        }
	
        public function __get($name=''){
            $value = null;
            if(property_exists($this, $name)){
                $value = $this->$name;
            }
            return $value;
        }
    }
	
    $order = new order();
    $order->OrderID = '201305062202';
    $order->OrderAmount = 23.45;
    $order->InvalidMember = 'Missed Assignment';
	
    echo '<pre>', print_r($order, true), '</pre>';


```
#### Example-3

> 调用对象不存在的方法时请区分静态方法和非静态方法，静态方法用`__callstatic()`调用，非静态方法用`__call()`调用。而且非静态方法只能通过类的实例化对象调用，否则会报错。

```php
<?php 

    class TestClass { 
        function __call($method, $args) { 
            echo "Non-Static Method {$method} with args: " . print_r($args, TRUE); 
        } 
        static function  __callstatic($method, $args) { 
            echo "Static Method {$method} with args: " . print_r($args, TRUE); 
        } 
    } 

    $obj = new TestClass(); 
    $obj->method_doesnt_exist('Non-static Method'); 
    echo "<br/>";
    TestClass::method_doesnt_exist('Static Method'); 


```


### Final关键字

- 如果父类中的方法被声明为 final，则子类无法覆盖该方法。如果一个类被声明为 final，则不能被继承。

- 属性不能被定义为 final，只有类和方法才能被定义为 final。

#### Example-1

> 类的私有成员不能被继承，所以子类中可以定义父类的同名方法，但是如果父类定义了 `private final` 方法，子类中一定不要出现与同类同名的方法，否则会报错。


```php
<?php
    class A {
        private final function method(){}     
    }

    class B extends A {
        private function method(){}
    }


```

### 后期静态绑定

"后期绑定"的意思是说，static:: 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。也可以称之为"静态绑定"，因为它可以用于（但不限于）静态方法的调用。

#### Example-1

> 后期静态绑定的解析会一直到取得一个完全解析了的静态调用为止。另一方面，如果静态调用使用 `parent:: ` 或者 ` self::` 将转发调用信息。

```php
<?php
    class A {
        public static function foo() {
            static::who();
        }

        public static function who() {
            echo __CLASS__."\n";
        }
    }

    class B extends A {
        public static function test() {
            A::foo();
            parent::foo();
            self::foo();
        }

        public static function who() {
            echo __CLASS__."\n";
        }
    }

    class C extends B {
        public static function who() {
            echo __CLASS__."\n";
        }
    }

    C::test(); // outputs A C C
    B::test(); // outputs A B B


```

### 对象和引用
PHP 的引用是别名，就是两个不同的变量名字指向相同的内容。在php5，一个对象变量只是保存一个**标识符**来访问真正的对象内容。 

#### Example-1

> 当对象作为参数传递，作为结果返回，或者赋值给另外一个变量，另外一个变量跟原来的不是引用的关系，只是他们都保存着同一个**标识符的拷贝**，这个标识符指向同一个对象的真正内容。
> 
> 请注意unset删除对象和给对象赋值为null的区别。 

```php
<?php
    class A {
        public $foo = 1;
    }  

    $a = new A;
    $b = $a;
    $a->foo = 2;
    $a = NULL;
    echo $b->foo."<br/>\n"; // 2

    $c = new A;
    $d = &$c;
    $c->foo = 2;
    $c = NULL;
    echo $d->foo."<br/>\n"; // Notice:  Trying to get property of non-object...

    $e = new A;
    $f = &$e;
    $e ->foo = 2;
    unset($e);
    echo $f->foo."<br/>\n"; // 2


```

### 序列化和反序列化

所有php里面的值都可以使用函数serialize()来返回一个包含字节流的字符串来表示。unserialize()函数能够重新把字符串变回php原来的值。 序列化一个对象将会保存对象的所有变量，但是不会保存对象的方法，只会保存类的名字。

#### Example-1

> 对象的静态成员不能被序列化，`protected`类型的受保护属性前加`*`做前缀，`private`类型的私有属性前加类名做前缀，反序列化后只保存对象的变量，不保留方法。


```php
// file "classa.php"
<?php
    class A{
        public $one = 1;
        static $two = 2; //静态成员并没有存储在序列化文件中
        protected $three =3;
        private $four = 4;

        public function show_one(){
            echo $this ->one;
        }
        public function show_two(){
            echo self::$two;
        }
    }


```
```php
// file "page1.php"
<?php
	
    include "classa.php";

    $a = new A;
    $s = serialize($a);

    file_put_contents('store.txt', $s);
    echo $s; 
    //O:1:"A":3:{s:3:"one";i:1;s:8:"*three";i:3;s:7:"Afour";i:4;}


```
```php
// file "page2.php"
<?php
	
    include 'classa.php';

    $s = file_get_contents('store.txt');
    $a = unserialize($s);

    $a -> show_one();
    $a -> show_two();
    var_dump($a) ; 

/* object(A)#1 (3) { 
        ["one"]=> int(1) 
        ["three":protected]=> int(3) 
        ["four":"A":private]=> int(4) 
    }
*/


```

---
