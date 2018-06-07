---
layout: post
title:  "PHP echo,print,print_r,var_dump 区别"
date:   2018-6-7 10:00:02 +0800
author: "南丞"
desc: "PHP echo,print,print_r,var_dump 区别"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---
### PHP echo(),print(),print_r(),var_dump() 区别解析

#### echo

echo是语言结构(language construct)，而并不是真正的函数，没有返回值。特点：

- 和print唯一不同之处，echo接受参数列表。

- 输出多个参数，不换行。

- 注意：如果参数中包含变量，需要使用双引号指明参数，否则输出的是变量名，而不是变量值。如下所示：

```php
    <?php  
        $a = echo("55nav"); // 错误！不能用来赋值  
        echo "55nav"; // 55nav   
        echo ("55nav"); // 55nav   
        echo ("55nav","com"); //发生错误，有括号不能传递多个参数   
        echo "55nav"," com"," is", " web";  // 不用括号的时候可以用逗号隔开多个值， 会输出 55nav com is web   
        echo "55nav is  8 good  9 web.";  // 不管是否换行，最终显示都是为一行 55nav is good web.  
        $fistname="55nav"; 
        echo "$fistname com"; // 如果 $firstname = "55nav", 则会输出 55nav com.  
        echo '$firstname com'; // 由于使用单引号，所以不会输出$firstname的值，而是输出 $firstname com 

        
```

#### print() 

pirnt()实际上也不是函数（而是语言结构），所以可以不用圆括号包围参数列表。

*特点:*

- 和echo的唯一区别：print()只支持一个参数

- 和echo的第三点一样，即是：如果参数中包含变量，需要使用双引号指明参数，否则输出的是变量名，而不是变量值。如下所示:

```php
    <?php
        print "bar is $bar"; //bar is barbaz  
        print 'bar is $bar'; //bar is $bar 
        
        //多个参数会报错
        print '1','abc','cdef';
        //Parse error: syntax error, unexpected ',' in /Users/nancheng/www/test.php on line 3

        $a = print("55nav"); // 这个是允许的  
        echo $a; // $a的值是1 

```

#### printf()
printf函数返回一个格式化后的字符串。

参数 format 是转换的格式，以百分比符号 (“%”) 开始到转换字符结束。下面是可能的 format 值：
* %% – 返回百分比符号
* %b – 二进制数
* %c – 依照 ASCII 值的字符
* %d – 带符号十进制数
* %e – 可续计数法（比如 1.5e+3）
* %u – 无符号十进制数
* %f – 浮点数(local settings aware)
* %F – 浮点数(not local settings aware)
* %o – 八进制数
* %s – 字符串
* %x – 十六进制数（小写字母）
* %X – 十六进制数（大写字母）
arg1, arg2, arg++ 等参数将插入到主字符串中的百分号 (%) 符号处。该函数是逐步执行的，在第一个 % 符号中，插入 arg1，在第二个 % 符号处，插入 arg2，依此类推。如果 % 符号多于 arg 参数，则您必须使用占位符。占位符被插入 % 符号之后，由数字和 “\$” 组成。

*例如:*

```php
    <?php 
    printf("My name is %s %s。","55nav", "com"); // My name is 55nav com。 
    printf("My name is %1\$s %1\$s","55nav", "com"); // 在s前添加1\$或2\$.....表示后面的参数显示的位置，此行输出 My name is 55nav 55nav因为只显示第一个参数两次。 
    printf("My name is %2\$s %1\$s","55nav", "com"); // My name is com 55nav  

```

#### sprintf()

此函数使用方法和printf一样，唯一不同的就是该函数把格式化的字符串写写入一个变量中，而不是输出来。

```php
    <?php 
        sprintf("My name is %1\$s %1\$s","55nav", "com");  //你会发现没有任何东西输出的。  
        $out = sprintf("My name is %1\$s %2\$s","55nav", "com");  
        echo $out;  //输出 My name is 55nav com  

```


#### print_r()

可以把字符串和数字简单地打印出来，而数组则以括起来的键和值得列表形式显示，并以Array开头。但print_r()输出布尔值和NULL的结果没有意义，因为都是打印"\n"。因此用var_dump()函数更适合调试。

> print_r() 返回值是布尔型的,参数是mix类型的,可以是字符串,整形,数组,对象类print_r() 显示关于一个变量的易于理解的信息。如果给出的是 string、integer 或 float，将打印变量值本身。如果给出的是 array，将会按照一定格式显示键和元素。object 与数组类似。 
print_r() 将把数组的指针移到最后边。 

```php
    <?php
     print_r("\nabcdef");
     $a="55nav"; 
     $c = print_r($a);  
     echo $c;  // $c的值是TRUE  
     $c = print_r($a,true);  
     echo $c; // $c的值是字符串55nav  

```

#### var_dump()

判断一个变量的类型与长度,并输出变量的数值,如果变量有值输的是变量的值并回返数据类型。此函数显示关于一个或多个表达式的结构信息，包括表达式的类型与值。数组将递归展开值，通过缩进显示其结构。

```php
<?php
 $arr = [1,2,3,4];
  var_dump($arr);

```
>var_dump()和print_r()的区别

> 共同点：两者都可以打印数组，对象之类的复合型变量。

> 区别：print_r() 只能打印一些易于理解的信息，且print_r()在打印数组时，会将把数组的指针移到最后边，使用 reset() 可让指针回到开始处。 而var_dump()不但能打印复合类型的数据，还能打印资源类型的变量。且var_dump()输出的信息则比较详细，一般调试时用得多。