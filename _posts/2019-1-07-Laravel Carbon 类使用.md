---
layout: post
title:  "Laravel Carbon 类使用"
date:   2019-1-07 10:00:02 +0800
author: "南丞"
desc: "Laravel Carbon 类使用"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---

# Laravel Carbon 类使用

#### Carbon 时间处理应用:

```php
        echo Carbon::now();                                                 // 获取当前时间
        echo Carbon::now('Arctic/Longyearbyen');                         //获取指定时区的时间
        echo Carbon::now(new \DateTimeZone('Europe/London'));            //获取指定时区的时间
        echo Carbon::today();                                               //获取今天时间 时分秒是 00-00-00
        echo  Carbon::tomorrow('Europe/London');                         // 获取明天的时间
        echo Carbon::yesterday();                                           // 获取昨天的时间
        echo Carbon::now()->timestamp;                                           // 获取当前的时间戳
        //以上结果输出的是一个Carbon 类型的日期时间对象
        /**
         * Carbon {#179 ▼
         * +"date": "2016-06-14 00:00:00.000000"
         * +"timezone_type": 3
         * +"timezone": "UTC"
         * }
         */
```

####  获取一个字符串

```php
        echo Carbon::today()->toDateTimeString();                            // 获取当天的时间 带时分秒
        echo Carbon::yesterday()->toDateTimeString();
        echo Carbon::tomorrow()->toDateTimeString();
        echo Carbon::today()->toDateString();                                // 获取当天的时间 不带时分秒
        echo Carbon::yesterday()->toDateString();
        echo Carbon::tomorrow()->toDateString();
```
        
#### 日期解析:

```php
        echo Carbon::parse('2018-12-27')->toDateString();             //2018-12-27
        echo Carbon::parse('2016-12-27')->toDateTimeString();         //2018-12-27 00:00:00
        echo Carbon::parse('2016-12-27 00:10:25')->toDateTimeString(); //2018-12-27 00:10:25
        echo Carbon::parse('2016-12-27 00:10:25')->timestamp;       //获取某个日期的时间戳
        echo Carbon::parse('today')->toDateTimeString();            //获取当天的时间
        echo Carbon::parse('yesterday')->toDateTimeString();        //获取昨天的时间
        echo Carbon::parse('tomorrow')->toDateTimeString();         // 获取明天的时间
        echo Carbon::parse('2 days ago')->toDateTimeString();       // 获取两天之前的时间
        echo Carbon::parse('+3 days')->toDateTimeString();       // 获取两天之后的时间
        echo Carbon::parse('+2 weeks')->toDateTimeString();      // 获取两周之后的时间
        echo Carbon::parse('+4 months')->toDateTimeString();      // 获取两月之后的时间
        echo Carbon::parse('-1 year')->toDateTimeString();      // 获取上一年的今天时间
        echo Carbon::parse('next wednesday')->toDateTimeString(); // 获取下周三的时间
        echo Carbon::parse('last friday')->toDateTimeString();    // 获取最后一个周五的时间
        var_dump(Carbon::now()->isWeekday());  //是否是工作日
        var_dump(Carbon::parse('2018-12-29')->isWeekday());
        var_dump(Carbon::now()->isWeekend());  //是否是休息日
        var_dump(Carbon::parse('2018-12-29')->isWeekend());
        var_dump(Carbon::parse('2018-12-26')->isYesterday()); //是否是昨天
        var_dump(Carbon::parse('2018-12-26')->isToday()); //是否是今天
        var_dump(Carbon::parse('2018-12-26')->isTomorrow()); //是否是明天
        var_dump(Carbon::createFromDate(1991,10,24)->isBirthday()); //今天是否是生日
```

#### 构造日期

```php
        echo Carbon::createFromDate('2018', '12', '27');  //2018-12-27 02:21:53   构造年月
        echo Carbon::createFromDate('2018', '12', '27','Asia/Taipei');  //2018-12-27 10:22:12  //根据时区构造年月
        echo Carbon::createFromTime('12', '59', '52');   // 给当前时间构造时分秒
        echo Carbon::create('2018', '12', '27', '12', '59', '52', 'Asia/Taipei'); //构造完整的时分秒
        echo Carbon::createFromFormat('Y/m/d H', '2018/05/21 22')->toDateTimeString(); // 构造指定格式的时间
        echo Carbon::createFromTimeStamp(1545877701)->toDateTimeString();    //指定时间戳构建时间
        echo Carbon::createFromTimeStamp(1545877701,'Asia/Taipei')->toDateTimeString();    //指定时间戳和时区构建时间
        echo Carbon::createFromTimeStampUTC(1545877701)->toDateTimeString();    //指定时间戳和时区构建时间

```

#### 获取日期信息:

```php
        $time = Carbon::now();
        var_dump($time->year);   //获取当前的年
        var_dump($time->month);   //获取当前的月
        var_dump($time->day);   //获取当前的日
        var_dump($time->hour);   //获取当前的时
        var_dump($time->minute);   //获取当前的分
        var_dump($time->second);   //获取当前的秒
        var_dump($time->micro);   //获取当前的毫秒
        var_dump($time->dayOfWeek);   //获取当前时间是这周的第几天
        var_dump($time->dayOfYear);   //获取当前时间是今年的第几天
        var_dump($time->weekOfMonth);   //获取当前时间所以在周是当前月的第几周
        var_dump($time->weekOfYear);   //获取当前时间所以在周是当前年的第几周
        var_dump($time->daysInMonth);   //获取当月是多少天
        var_dump(Carbon::createFromDate(1991, 10, 24)->age); //获取当前时间 距离 1991,10,24 多少年, 也就是你多少岁了

```

#### 日期操作:

```php
        echo Carbon::now()->addDays(25);   //当前时间加上25天
        echo Carbon::now()->addWeeks(3);   //当前时间加上3周
        echo Carbon::now()->addHours(25);   //当前时间加25小时
        echo Carbon::now()->subHours(2);    //当前时间减2小时
        echo Carbon::now()->addHours(2)->addMinutes(12);
        echo Carbon::now()->modify('+25 days'); //当前时间加上25天
        echo Carbon::now()->modify('-2 days'); //当前时间减2天
```

#### 日期比较:

```php
        echo Carbon::now()->tzName;   //获取当前时区
        $first = Carbon::create(2018, 12, 5, 23, 26, 11);
        $second = Carbon::create(2018, 9, 5, 20, 26, 11);
        echo $first->toDateTimeString();
        echo $second->toDateTimeString();
        /**
         * min –返回最小日期。
         * max – 返回最大日期。
         * eq – 判断两个日期是否相等。
         * gt – 判断第一个日期是否比第二个日期大。
         * lt – 判断第一个日期是否比第二个日期小。
         * gte – 判断第一个日期是否大于等于第二个日期。
         * lte – 判断第一个日期是否小于等于第二个日期。
         */
        var_dump($first->eq($second));
        var_dump($first->ne($second));
        var_dump($first->gt($second));
        var_dump($first->gte($second));
        var_dump($first->lt($second));
        var_dump($first->lte($second));
        //区间比较
        $first = Carbon::create(2018, 10, 5, 1);
        $second = Carbon::create(2018, 10, 5, 5);
        var_dump(Carbon::create(2018, 10, 5, 3)->between($first, $second));          // bool(true)
        var_dump(Carbon::create(2018, 10, 5, 5)->between($first, $second));          // bool(true)
        var_dump(Carbon::create(2018, 10, 5, 5)->between($first, $second, false));   // bool(false)

```

#### 获取特殊时间:

```php
        echo Carbon::now()->startOfDay();  //今天开始时间
        echo Carbon::now()->endOfDay();  //今天结束时间
        echo Carbon::now()->startOfWeek(); //这周开始时间紧
        echo Carbon::now()->endOfWeek(); //这周结束时间
        echo Carbon::now()->startOfMonth(); //这月开始时间
        echo Carbon::now()->endOfMonth(); //这月开始时间

```        