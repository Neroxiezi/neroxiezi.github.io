---
layout: post
title:  "Laravel 多态关联(morphTo,morphMany)"
date:   2018-8-31 10:00:02 +0800
author: "南丞"
desc: "Laravel 多态关联(morphTo,morphMany)"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---

# Laravel 多态关联(morphTo,morphMany)

在网站开发的过程中,经常会遇到 评论商品，评论文章, 评论店铺 等等,在处理这样的需求的时候, 经常会新建一张 评论表, 然后通过 一个 type字段来区分 评论的对象 开发过程如下:

#### 新建表操作

```php
php artisan make:model Models/Comments -m
```
- 表字段:

```php
    public function up()
    {
        Schema::create('comments', function (Blueprint $table) {
            $table->increments('id');
            $table->timestamps();
            $table->integer('member_id');
            $table->string('comment_object_type');   # 评论对象
            $table->integer('comment_object_id');    # 评论对象的id
            $table->text('comment_content');         # 评论内容
            $table->tinyInteger('status');
        });
    }

```
做数据迁移:
```php
php artisan migrate
```

### 造数据

1. 用户 ID 为2和4  的用户对 商品ID 为 1,2,3,4的商品进行评论

```sql
INSERT INTO `comments`(`member_id`,`comment_object_type`,`comment_object_id`,`status`,`created_at`,`updated_at`)
VALUES
(2,'App\\Models\\Goods',1,0,'2018-09-07 15:58:04','2018-09-07 15:58:04'),
(2,'App\\Models\\Goods',2,0,'2018-09-07 15:58:04','2018-09-07 15:58:04'),
(2,'App\\Models\\Goods',3,0,'2018-09-07 15:58:04','2018-09-07 15:58:04'),
(2,'App\\Models\\Goods',4,0,'2018-09-07 15:58:04','2018-09-07 15:58:04'),
(4,'App\\Models\\Goods',3,0,'2018-09-07 15:58:04','2018-09-07 15:58:04'),
(3,'App\\Models\\Goods',4,0,'2018-09-07 15:58:04','2018-09-07 15:58:04')
```
2. 用户ID 为2 的用户 对 店铺ID 为 1,4 的 店铺进行了评论
```sql
INSERT INTO `comments`(`member_id`,`comment_object_type`,`comment_object_id`,`status`,`created_at`,`updated_at`)
VALUES
(2,'App\\Models\\Stores',1,0,'2018-09-07 15:58:04','2018-09-07 15:58:04'),
(2,'App\\Models\\Stores',4,0,'2018-09-07 15:58:04','2018-09-07 15:58:04'),
```

### 查询
- 数据造完毕, 接下来要做查询,查询一下 商品id为2的 所有评论, 并且查询出评论人的信息

#### 普通查询

```php
   public function comment_list(Requset $request, Goods $goods)
  {
        # 查询商品的所有评论
        $comments = Comment::where('comment_object_type',Goods::class)->where('comment_object_id',$goods->id)->get();
       if($comments) {
          foreach($comments as $comment) {
                 $comment->member = Member::find('id',$comment->member_id)    
           }
       }
       dd($comments)
   }

```

#### 普通连表查

**Comment.php  文件**
```php
   # Comment model 文件修改
   # 查找评论的用户的信息
   public function member()
    {
        return $this->belongsTo(Member::class, 'comment_member_id');
    }

```
**需求的查询**

```php
   public function comment_list(Requset $request, Goods $goods)
  {
        # 查询商品的所有评论
        $comments = Comment::where('comment_object_type',Goods::class)->where('comment_object_id',$goods->id)->get();
        # 省掉了 循环 在模板遍历的时候 直接调用  $item->member 查看用户信息
       dd($comments)
   }

```

#### 多态查询

**Comment.php  文件**

```php
 # Comment model 文件修改
 # 评论对象 
   public function comment_object()
    {
        return $this->morphTo();
    }

   # 查找评论的用户的信息
   public function member()
    {
        return $this->belongsTo(Member::class, 'comment_member_id');
    }
```
**Goods.php  文件**

```php
 # 商品关联评论
    public function comments()
    {
        return $this->morphMany(Comment::class,self::class,'comment_object_type','comment_object_id');
    }
```

**需求的查询**

```php
 public function comment_list(Requset $request, Goods $goods)
  {
        # 查询商品的所有评论
        $comments =$goods->comments()->with('member')->paginate(15);
        dd($comments)
   }

```