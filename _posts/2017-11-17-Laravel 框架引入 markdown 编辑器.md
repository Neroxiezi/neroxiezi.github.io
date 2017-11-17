---
layout: post
title:  "Laravel 框架引入 markdown 编辑器"
date:   2017-11-17 16:40:12 +0800
author: "南丞"
desc: "Laravel是一套简洁、优雅的PHP Web开发框架(PHP Web Framework)。它可以让你从面条一样杂乱的代码中解脱出来；它可以帮你构建一个完美的网络APP，而且每行代码都可以简洁、富于表达力。"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---
# 引入 markdown 编辑器packages

- **首先在 composer.json 的 require 里面加入以下内容**

```php
  "yuanchao/laravel-5-markdown-editor": "dev-master"
```

2.添加完成后，执行 composer update

```php
  composer update
```
3.执行完这行命令，基本上就安装完啦，接下来打开 config/app.php 往里面加点东西

// 往里面加入
```php
'YuanChao\Editor\EndaEditorServiceProvider'

'providers' => [

        ........

        'YuanChao\Editor\EndaEditorServiceProvider'

        ........

    ],

// 往里面加入 `'EndaEditor' => 'YuanChao\Editor\Facade\EndaEditorFacade'`

'aliases' => [

     ..........

    'EndaEditor' => 'YuanChao\Editor\Facade\EndaEditorFacade'

    ..........

    ],
```

4.加入以上配置以后，执行 php artisan vendor:publish --force
```php
// 加上 --force 覆盖配置文件
php artisan vendor:publish --force
```

执行上面这行命令以后，如果出现以下结果，说明你已经安装成功了

```
Copied File [/vendor/edvinaskrucas/notification/src/config/notification.php] To [/config/notification.php]
Copied Directory [/vendor/yuanchao/laravel-5-markdown-editor/src/config/views] To [/resources/views/vendor/editor]
Copied Directory [/vendor/yuanchao/laravel-5-markdown-editor/src/config/editor] To [/public/plugin/editor]
Copied File [/vendor/yuanchao/laravel-5-markdown-editor/src/config/editor.php] To [/config/editor.php]
Publishing Complete!
```

#### 使用
安装完以后，说下如何使用

**注意！你必须先引入 jquery 在引入头部文件之前**

在你的视图文件里面，需要使用编辑器的地方，先引入头部文件

```html
  // 引入编辑器代码
  @include('editor::head')

```
> 然后，比如我的 textarea 需要把他变成编辑器，这个时候，给你的 textarea 写上一个 id = myEditor


 //如：
 > < textarea id='myEditor'>< /textarea>


> 同时，在你的 textarea 的父级标签，加上 class = editor,如果你的 textarea 是被 div 包住的，那么你就这样写

```html
// class = editor
<div class="editor">
    <textarea id='myEditor'></textarea>
</div>
```

**一定要给父级标签加上 class = editor,因为要计算编辑器的位置**

> 看了上面的例子，你应该明白，使用编辑器的两步就是

- **引入头文件**
> 给你需要变成编辑器的标签加上 id='myEditor' 同时父级标签加上 class = editor

- **图片上传使用**

> 打开 config/editor.php 配置文件，我们可以看到里面有一项配置是 uploadUrl
这个 uploadUrl ，指得是上传图片请求的地址，我们上传图片时，是通过 Ajax post 请求 控制器，控制器把图片上传，返回图片地址
比如，我的 uploadUrl 配置成了 Home/upload ,那么当我上传图片的时候，它会 Ajax 请求 Home 控制器的 upload 方法对吧？这个时候 upload 方法里面，需要把图片上传，然后返回图片地址
但是！我们的扩展里面已经集成了图片上传并返回编辑器所需要的数据了，所以在你的 upload 方法里面，直接调用扩展的方法就行啦！

首先，在类头部，添加引用

```php
use EndaEditor;


public function upload(){

        // path 为 public 下面目录，比如我的图片上传到 public/uploads 那么这个参数你传uploads 就行了

        $data = EndaEditor::uploadImgFile('path');

        return json_encode($data);

    }

```
这个 upload 是专门用于编辑器图片上传的哦～

好了，这个时候，你的图片上传已经 ok 了！

### markdown 转 html

我们使用编辑器，把内容插入数据库后，展示给用户看的时候，总不能就输出 markdown 的语法吧～那用户也看不懂呀

所以，你需要把 markdown 转成 html，这样游览器才会解析

那么问题来了，如何转呢？我们的扩展已经集成了这个功能。我们来使用下

依旧是头部引入
首先，在类头部，添加引用

```php
   use EndaEditor;
```
然后，调用我们的方法就行啦


// 直接把需要转换的 markdown 做为参数传递进去
```php
$str = EndaEditor::MarkDecode("#我是markdown语法");

echo $str;
```
// 结果为

```html

<h1>我是markdown语法</h1>

```
