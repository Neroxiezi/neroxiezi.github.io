---
layout: post
title:  "CGI,FAST-CGI,PHP-FPM的区别"
date:   2018-5-23 10:00:02 +0800
author: "南丞"
desc: "Web 服务器运行时外部程序的几种方式"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---
# CGI,FAST-CGI,PHP-FPM的区别

### CGI

**CGI** 全称Common Gateway Interface即公共网关接口，它遵循cgi规范，定义了与其他服务之间的通信协议，

``PHP以CGI方式运行时，当一个请求向web server请求动态页时，``
``web server总会fork一个CGI解释器进程进行处理这个请求，进程处理完成之后将结果返回给web server，``
``web server将结果返回并显示出来，进程结束，当用户再次请求同一个页面时，``
``web server总会fork一个进程进行处理，这样效率会比较低下（CGI被人诟病的主要原因）``

### FAST-CGI

 为了解决这个问题，所以有了FAST-CGI的出现。

``FAST-CGI顾名思义是要提高CGI的效率，它主要处理CGI重复初始化的过程。``

先看下FAST-CGI的原理：

- 1、Web Server 启动时加载FAST-CGI的进程管理器，如apache module
- 2、FASTCGI进程管理器自身初始化，启动多个php-cgi（CGI解释器进程），并等待来自web server的链接

- 3、接收到请求时，FastCgi选择并链接到一个php-cgi，将CGI的环境变量和标准输入发送到FastCgi的php-cgi进程
- 4、处理完成后，php-cgi将标准输出和错误信息返回web server，并断开与FastCgi的连接，等待下一次请求接入。如果是CLI模式下，则php-cgi直接退出。


FastCgi的缺点：

启动多个进程，占用内存，一个php-cgi程序通常占用7-25M内存，如果在很多请求并发量下，内存消耗严重。

``PHP-CGI是FastCgi的管理进器，它的不足：``

- 1、修改php.ini文件要重启php-cgi才能生效，不能平滑重启
- 2、直接杀死php-cgi，php不能运行

### PHP-FPM

PHP-FPM是一个PHP FastCGI管理器，是只用于PHP的，可以在 http://php-fpm.org/download下载得到。

PHP-FPM其实是PHP源代码的一个补丁，旨在将FastCGI进程管理整合进PHP包中。必须将它patch到你的PHP源代码中，在编译安装PHP后才可以使用。

PHP-FPM 这种模型是非常典型的多进程同步模型，意味着一个请求对应一个进程线程，并且 IO 是同步阻塞的。所以尽管 PHP-FPM 维护着独立的 CGI 进程池、系统也可以很轻松的管理进程的生命周期，

受制于服务器的硬件设施，PHP-FPM 需要指定合理的 php-fpm.conf 配置：

```
pm.max_children # 子进程最大数
pm.start_servers # 启动时的子进程数
pm.min_spare_servers # 最小空闲进程数，空闲进程不够时自动补充
pm.max_spare_servers # 最大空闲进程数，空闲进程超过时自动清理
pm.max_requests = 1000 # 子进程请求数阈值，超过后自动回收
```
