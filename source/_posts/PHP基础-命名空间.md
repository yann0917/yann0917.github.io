---
title: PHP基础-命名空间
tags: php
categories: php
abbrlink: 22141
date: 2017-04-29 13:44:15
---


> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

在PHP中，命名空间用来解决在编写类库或应用程序时创建可重用的代码如类或函数时碰到的两类问题： 

1. 用户编写的代码与PHP内部的类/函数/常量或第三方类/函数/常量之间的名字冲突。  
2. 为很长的标识符名称(通常是为了缓解第一类问题而定义的)创建一个别名（或简短）的名称，提高源代码的可读性。

```php
<?php
     namespace NS;

     define(__NAMESPACE__ .'\foo','111');
     define('foo','222');

     echo foo;  // 111.
     echo \foo;  // 222.
     echo \NS\foo;  // 111.
     echo NS\foo;  // fatal error. assumes \NS\NS\foo.


```

只有 `类（包括抽象类和traits）、接口、函数和const定义的常量` 类型的代码受命名空间的影响。

同一个命名空间可以定义在多个文件中，即允许将同一个命名空间的内容分割存放在不同的文件中。

所有非 PHP 代码包括空白符都不能出现在命名空间的声明之前。

#### Example-1

> PHP 命名空间允许使用反斜杠 `\` 指定层次化的命名空间的名称。
> 命名空间不可以嵌套，但是可以使用子命名空间，达到相同的效果。

```php
<?php
     namespace my\stuff {
          namespace nested {//错误，命名空间不可以嵌套
               class foo {}
          }
     }
     /*
       namespace my\stuff\nested {
            class foo {}
       }
     */

```

#### Example-2

> 在同一个文件中定义多个命名空间有两种语法形式：简单组合语法与大括号语法，但是两种风格在一个文件中不能混合使用。
>
> 如果使用大括号语法，请注意花括号`{}`外面不允许有任何代码。结束标签 `?>` 后面如果有空格或者是换行则会报错，因此建议在纯PHP代码的文件中不要写PHP结束标签 `?>`。

```php
<?php
     namespace MyProject; //简单组合语法
     const CONNECT_OK = 1;
     class Connection { /* ... */ }
     function connect() { /* ... */  }
     namespace AnotherProject{ //大括号语法
     const CONNECT_OK = 1;
     class Connection { /* ... */ }
     function connect() { /* ... */  }
     }
 　
```

#### Example-3

> `use` 关键字必须在文件全局范围或者是已经声明的其他命名空间里，不可以在块作用域里，这是因为外部命名空间导入是在编译时而不是运行时完成的。

```php
<?php
     namespace Languages;
     function toGreenlandic(){
          use Languages\Danish;
     // ...
     }


```

#### Example-4

> PHP5.6 之前只能通过use导入命名空间和类，PHP 5.6 之后允许导入常量和方法。
> PHP 7.0 后支持分组导入命名空间。

```php
<?php
     // Pre PHP 7.0 code
     use some\Mynamespace\ClassA;
     use some\Mynamespace\ClassB;
     use some\Mynamespace\ClassC as C;

     use function some\Mynamespace\fn_a;
     use function some\Mynamespace\fn_b;
     use function some\Mynamespace\fn_c;

     use const some\Mynamespace\ConstA;
     use const some\Mynamespace\ConstB;
     use const some\Mynamespace\ConstC;

     // PHP 7+ code
     use some\Mynamespace\{ClassD, ClassE, ClassF as F};
     use function some\Mynamespace\{fn_d, fn_e, fn_f};
     use const some\Mynamespace\{ConstD, ConstE, ConstF};


```

---
