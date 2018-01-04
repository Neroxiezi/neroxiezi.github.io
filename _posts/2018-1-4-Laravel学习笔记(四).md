---
layout: post
title:  "Laravel框架学习笔记(四)"
date:   2018-1-4 21:30:02 +0800
author: "南丞"
desc: "Laravel是一套简洁、优雅的PHP Web开发框架(PHP Web Framework)。它可以让你从面条一样杂乱的代码中解脱出来；它可以帮你构建一个完美的网络APP，而且每行代码都可以简洁、富于表达力。"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---
# Laravel框架学习笔记(四)之 数据同步操作(sync()的使用)

闲话不多说,直接起飞:

使用场景：

>  在使用laravel框架开发的过程中,我们经常会遇到 三张表 之间 有关联的操作,比如如下:

>  表1:  店铺表 store

|字段|类型|描述|
|----|----|----|
|store_id|int|主键|
|store_name|varchar(255)|名称|

> 表2: 店铺标签表 tag

|字段|类型|描述|
|----|----|----|
|tag_id|int|主键|
|tag_name|varchar(255)|属性名称|

> 表3: 店铺与标签的关系表 tag_group

|字段|类型|描述|
|----|----|----|
|tag_id|int|标签id|
|store_id|int|店铺id|

**注意:店铺表与标签表示多对多的关系** 

表列出来以后,然后我们会经常遇到一个这样的操作:

**添加一个店铺,并且会在添加的时候给店铺挂载标签**

面对这样的需求 我们常规的做法是如下:


```php 
<?php
//首先从表单中传递过来一个数组:
$param['store_name']='测试店铺';//这是接收的店铺名字
$data['tag_ids'] = [1,3,4,5];//这是选择的标签的id数组  

//然后首先 店铺表中插入一条数据
$result = Store::create($param);  
//var_dump($result);

//遍历选择的标签数组 绑定标签与店铺之间的关系
if(count($data['tag_ids'])>0) {
    foreach ($data['tag_ids'] as $value) {
        $res[] = TagGroup::create(['store_id'=>$result->store_id,'tag_id'=>$value]); //绑定关系
    }
}
?>```

按照上面的代码写下来 估计在修改的店铺标签的时候 估计会炸掉! 因为首先要查 这个标签绑定过没 绑定了的 要删除 并把没有绑定过的再绑定上 多麻烦 所以 有需求就有解决方案! 如下所示我们使用 sync() 来做关系:


```php
<?php
//首先从表单中传递过来一个数组:
$param['store_name']='测试店铺';//这是接收的店铺名字
$data['tag_ids'] = [1,3,4,5];//这是选择的标签的id数组  

//然后首先 店铺表中插入一条数据
$result = Store::create($param);  
//var_dump($result);

//这里我们不需要遍历了 只需要这样写
$res = $result->tag->sync($data['tag_ids']); 
?>

```

> 接下来我们需要在  Stroe model中 去定义一个关联模型


```php
<?php
class Store extend Model{
     public function tag() {
        return $this->belongsToMany(Tag::class,'tag_group','tag_id','store_id');
     }
    }
?>```

> 这样 我们就可以 很方便的去给店铺 挂载标签 或者属性, sync() 为我们提供了 大部分的功能,比如在修改的时候他会自动去找,如果没有的去绑定或者删除...








