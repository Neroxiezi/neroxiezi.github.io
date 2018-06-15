---
layout: post
title:  "PHP利用Redis锁解决并发访问"
date:   2018-6-15 10:00:02 +0800
author: "南丞"
desc: "PHP利用Redis锁解决并发访问"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---
### PHP利用Redis锁解决并发访问

- 并发访问限制问题

> 对于一些需要限制同一个用户并发访问的场景，如果用户并发请求多次，而服务器处理没有加锁限制，用户则可以多次请求成功。例如换领优惠券，如果用户同一时间并发提交换领码，在没有加锁限制的情况下，用户则可以使用同一个换领码同时兑换到多张优惠券。

*常见的业务逻辑代码如下：*

```
if A(可以换领)
   B(执行换领)
   C(更新为已换领)
D(结束)

```
如果用户并发提交换领码，都能通过可以换领(A)的判断，因为必须有一个执行换领(B)后，才会更新为已换领(C)。因此如果用户在有一个更新为已换领之前，有多少次请求，这些请求都可以执行成功。

- 并发访问限制方法

> 使用文件锁可以实现并发访问限制，但对于分布式架构的环境，使用文件锁不能保证多台服务器的并发访问限制。

具体的 redis 加锁类和示例代码如下：

```php
<?php

/**
 *  Redis 操作类
 *  Func:
 *  public  lock    获取锁
 *  public  unlock  释放锁
 *  private connect 连接
 */
class RedisLock
{ // class start

    private $_config;
    private $_redis;

    /**
     * RedisLock constructor.
     * @param array $config
     * @throws Exception
     */
    public function __construct($config = [])
    {
        $this->_config = $config;
        $this->_redis = $this->connect();
    }

    /**
     * 获取锁
     * @param  String $key 锁标识
     * @param  Int $expire 锁过期时间
     * @return Boolean
     */
    public function lock($key, $expire = 5)
    {
        $is_lock = $this->_redis->setnx($key, time() + $expire);

        // 不能获取锁
        if (!$is_lock) {

            // 判断锁是否过期
            $lock_time = $this->_redis->get($key);

            // 锁已过期，删除锁，重新获取
            if (time() > $lock_time) {
                $this->unlock($key);
                $is_lock = $this->_redis->setnx($key, time() + $expire);
            }
        }

        return $is_lock ? true : false;
    }

    /**
     * 释放锁
     * @param  String $key 锁标识
     * @return Boolean
     */
    public function unlock($key)
    {
        return $this->_redis->del($key);
    }

    /**
     * 链接redis
     * @return bool|Redis
     * @throws Exception
     */
    private function connect()
    {
        try {
            $redis = new \Redis();
            $redis->connect($this->_config['host'], $this->_config['port'], $this->_config['timeout'], $this->_config['reserved'], $this->_config['retry_interval']);
            if (empty($this->_config['auth'])) {
                $redis->auth($this->_config['auth']);
            }
            $redis->select($this->_config['index']);
        } catch (RedisException  $e) {
            throw new Exception($e->getMessage());
            return false;
        }
        return $redis;
    }
}

$config = array(
    'host' => 'localhost',
    'port' => 6379,
    'index' => 0,
    'auth' => '',
    'timeout' => 1,
    'reserved' => NULL,
    'retry_interval' => 100,
);

// 创建redislock对象
$oRedisLock = new RedisLock($config);

// 定义锁标识
$key = 'mylock';

// 获取锁
$is_lock = $oRedisLock->lock($key, 10);

if ($is_lock) {
    echo 'get lock success<br>';
    echo 'do sth..<br>';
    sleep(5);
    echo 'success<br>';
    //释放锁
    $oRedisLock->unlock($key);

// 获取锁失败
} else {
    echo 'request too frequently<br>';
}
```

保证同一时间只有一个访问有效，有效限制并发访问。



