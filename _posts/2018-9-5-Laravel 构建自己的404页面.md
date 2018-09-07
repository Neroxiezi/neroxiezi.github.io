---
layout: post
title:  "Laravel 构建自己的404页面"
date:   2018-9-5 10:00:02 +0800
author: "南丞"
desc: "Laravel 构建自己的404页面"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---

# Laravel 构建自己的404页面

首先,构建一个自己的404错误路由:

在路由文件中加入:

```php
Route::get('/error',function(){
    abort(404);
});
```

然后 在 reosouce/view 下 新建一个 errors 目录 然后在目录中 添加一个文件名问 **404.blade.php** 的自定义的 404模板文件,代码如下:

```html
<!DOCTYPE html>
<html>

<head>
    <title>404页面</title>
    <link href = "https://fonts.googleapis.com/css?family=Lato:100" rel = "stylesheet"
          type = "text/css">

    <style>
        html, body {
            height: 100%;
        }
        body {
            margin: 0;
            padding: 0;
            width: 100%;
            color: #B0BEC5;
            display: table;
            font-weight: 100;
        'Lato';
        }
        .container {
            text-align: center;
            display: table-cell;
            vertical-align: middle;
        }
        .content {
            text-align: center;
            display: inline-block;
        }
        .title {
            font-size: 72px;
            margin-bottom: 40px;
        }
    </style>

</head>
<body>

<div class = "container">
    <div class = "content">
        <div class = "title">404 错误</div>
    </div>
</div>

</body>
```
### 效果如下:

![image](https://user-images.githubusercontent.com/11400329/45200300-c1c29100-b2a2-11e8-9c0d-89a69d3002c0.png)

如果可以 可以把页面写的更好一点