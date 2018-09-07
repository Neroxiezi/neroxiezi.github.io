---
layout: post
title:  "Laravel 资源路由与注入"
date:   2018-9-6 10:00:02 +0800
author: "南丞"
desc: "Laravel 资源路由与注入"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---

### 资源路由

开发的过程中经常会使用资源路由:

```php
Route::resource('member','MemberController')
```

只用资源路由以后, 我们在写的资源控制器会是:

```php
class MemberController extends Controller
{
    public function index()
    {
        //
    }
    public function create()
    {
        //
    }

    public function store(Request $request)
    {
        //
    }

    public function show($id)
    {
        //
    }

    public function edit($id)
    {
        //
    }

    public function update(Request $request, $id)
    {
        //
    }

    public function destroy($id)
    {
        //
    }
}
```

然后会修改一下控制器,中的方法, 比如:

 **edit() **  方法: 按照以往的习惯,我们会传递一个 要编辑的数据的  **id**  然后去查询 这个  **id**   所对应的数据。但是这样有点麻烦,  我们可以 使用注入去写, 可以这样写:

```php
// Member 对应的我要查询的member  $member 这个 是我传递的id所对应的路由
 public function edit(Member $member)
 {
        dd($member)
}
``` 

注意: 经常会出现一个问题就是:  $member 这个变量名了, 资源路由中 这个变量名不一定是 resource 中的 member 名称了, 所以我们注入的时候先查询一下再写:


使用 命令查询所有的路由列表:

```
php artisan route:list 
```

结果如下:

```
+--------+-----------+----------------------+----------------+-----------------------------------------------------+--------------+
| Domain | Method    | URI                  | Name           | Action                                              | Middleware   |
+--------+-----------+----------------------+----------------+-----------------------------------------------------+--------------+
|        | GET|HEAD  | /                    |                | Closure                                             | web          |
|        | GET|HEAD  | api/user             |                | Closure                                             | api,auth:api |
|        | GET|HEAD  | member               | member.index   | App\Http\Controllers\Admin\MemberController@index   | web          |
|        | POST      | member               | member.store   | App\Http\Controllers\Admin\MemberController@store   | web          |
|        | GET|HEAD  | member/create        | member.create  | App\Http\Controllers\Admin\MemberController@create  | web          |
|        | GET|HEAD  | member/{member}      | member.show    | App\Http\Controllers\Admin\MemberController@show    | web          |
|        | PUT|PATCH | member/{member}      | member.update  | App\Http\Controllers\Admin\MemberController@update  | web          |
|        | DELETE    | member/{member}      | member.destroy | App\Http\Controllers\Admin\MemberController@destroy | web          |
|        | GET|HEAD  | member/{member}/edit | member.edit    | App\Http\Controllers\Admin\MemberController@edit    | web          |
+--------+-----------+----------------------+----------------+-----------------------------------------------------+--------------+

```

**{}** 中对应的路由参数就是那个变量所需要写的参数


