---
title: cookie and session
date: 2017-05-04 15:06:54
tags: 
 - cookie
 - session

categories: session
---
### 原理
#### cookie
cookie 存放于客户端，常用于识别用户。cookie是服务器留在用户计算机中的小文件。每当相同的计算机通过浏览器请求页面时，它同时会发送cookie.
#### session
session 存放于服务器端，用于存储有关用户会话的信息，或者更改用户会话的设置，其保存的信息是单一用户的，并且可供应用程序中的所有页面使用。它可以用于用户身份认证，程序状态记录，页面之间参数传递等。
#### session 的工作原理
当浏览器 第一次发送请求时，服务器自动生成了一个Session和一个Session ID用来唯一标识这个Session，并将其通过响应发送到浏览器，保存在cookie中。当浏览器第二次发送请求，会将前一次服务器响应中的Session ID放在请求中一并发送到服务器上，服务器从请求中提取出Session ID，并和保存的所有Session ID进行对比，找到这个用户对应的Session。

### cookie 与 session 的区别
- cookie数据保存在客户端，session数据保存在服务器端。
- 服务器也可以通过URL重写的方式来传递SessionID的值，因此不是完全依赖Cookie。如果客户端Cookie禁用，则服务器可以自动通过重写URL的方式来保存Session的值，并且这个过程对程序员透明。
- cookie 只能保存 ASCII 字符串，而session能够存储任何类型的数据；
- 安全性不同，cookie在客户端是可见的，session存储在服务器端相对客户端是透明的；

### 典型应用
- 判断用户是否登录过网站，以便下次登录的时候能够直接登录，如果删除了cookie则需要重新填写相关信息；
- 购物车中类的处理和设计。用户在一段时间内在同一家网站的不同页面选择不同的商品，或者是在用户未登录的时候将商品添加至购物车，在付款的时候从cookie中提取这些信息，需要考虑安全和性能问题。

### 常见问题
#### cookie禁用了，session 还能用吗？怎么使用？（session跨页传递）
- 通过 URL， 显式传输session ID.
- 通过表单隐藏域 POST数据到服务器端，每次传输session-ID.
- 在php.ini 或者 运行时配置以下项目，可以达到自动通过url传输 session_id
	`ini_set('session.use_only_cookies', '0'); // 不仅仅是用COOKIE传输session-ID`
	`ini_set('session.use_trans_sid', '1');   //自动通过 url 或者表单传输 session_id`


#### 在分布式数据库中session如何实现共享？
目前最优解是：使用 redis 分布式缓存存储session 。


### 友情链接
- [PHP Sessions](http://www.w3school.com.cn/php/php_sessions.asp)
- [PHP Cookies](http://www.w3school.com.cn/php/php_cookies.asp)
- [浅谈Session与Cookie的区别与联系](http://blog.csdn.net/duan1078774504/article/details/51912868)
- [PHPSession-完全PHP5之session篇](http://blog.csdn.net/masterft/article/details/1640122)
- [cookie和session](http://blog.csdn.net/xiajiandong1024/article/details/71038363#t10)
- [如何设置一个严格30分钟过期的Session ](http://www.laruence.com/2012/01/10/2469.html)
- [session多服务器共享的方案梳理](http://www.cnblogs.com/wangtao_20/archive/2013/10/29/3395518.html)


