---
layout: post
title:  "Laravel 数据迁移"
date:   2018-9-7 10:00:02 +0800
author: "南丞"
desc: "Laravel 数据迁移"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---

## Laravel 数据迁移
 通常我们在数据迁移的时候 一旦 mysql版本是5.7 的时候执行 **php artisan migrate** 命令的时候会出现
如下的错误:
```

  1   PDOException::("SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long; max key length is 1000 bytes")
      D:\phpStudy\WWW\Laravel-study\Laravel-Study\vendor\laravel\framework\src\Illuminate\Database\Connection.php:458

  2   PDOStatement::execute()
      D:\phpStudy\WWW\Laravel-study\Laravel-Study\vendor\laravel\framework\src\Illuminate\Database\Connection.php:458

```

造成这个错误的原因是因为laravel的配置稍微出点问题，可以修改一下配置文件：

原 **database.php** 中的配置:
```
'mysql' => [
            'driver' => 'mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'strict' => true,
            'engine' => null,
        ],
```
修改成:

```
'mysql' => [
            'driver' => 'mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8',
            'collation' => 'utf8_unicode_ci',
            'prefix' => '',
            'strict' => true,
            'engine' => null,
        ],
```
修改完以后, 然后先去数据库中 把 表删除了 然后跑迁移命令  结果如下:
```
Migration table created successfully.
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table
Migrating: 2018_09_05_022045_create_admins_table
Migrated:  2018_09_05_022045_create_admins_table
```
创建表成功。

另外解决办法:
不需要修改配置文件中的 默认字符集 我们修改一下  默认字符集的长度:
找到 **AppServiceProvider.php** 文件 然后做如下修改:

```
 public function boot()
    {
        Schema::defaultStringLength(191);
    }

```

然后在运行命令就行了



