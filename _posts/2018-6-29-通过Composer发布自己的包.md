---
layout: post
title:  "Composer发布自己的插件包"
date:   2018-6-29 10:00:02 +0800
author: "南丞"
desc: "Composer发布自己的插件包"

in_head: "<style> .article-content p{
  text-indent: 2em;
  line-height: 1.75rem;
}</style>"
---
### Composer发布自己的插件包

要发布自己的Composer 首先得整清楚composer.json文件的内容,下面是composer.json 的介绍:

```json
{
    "name": "nancheng/pfinal-array",      //包的名称
    "description": "This is a PHP array manipulation middleware",   //包的功能描述
    "authors": [                                                    //作者信息
        {
            "name": "南丞",
            "email": "Lampxiezi@163.com",
            "homepage": "http://friday-go.cc/"
        }
    ],
    "homepage":"https://github.com/Neroxiezi/pfinal-array",         //包的源码地址
    "license": "MIT",                                               //遵循的协议
    "keywords": [                                                   //包的关键字
        "pfinal",
        "南丞",
        "Q哥",
        "array",
        "php-array"
    ],

    "require": {                                                    //包的一些依赖
        "php": ">=5.4.0"
    },
    "autoload": {                                                   //自动加载
        "psr-4": {                                                  //自动使用的 psr-4 格式
            "pf\\arr\\": "src/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "example\\": "example"
        }
    }
}
```
弄清楚composer.json 以后 我们接下来开始整自己的包.

#### 包的目录结构:

```
    pfinal-array
        |
        |-- src    包源码
        |    |-- build  功能代码 
        |    |-- PFarr.php	包的门面
        |
        |-- example  一些实例
        |
        |-- README.md   
        |
        |-- composer.json
```
上面是我自己的包目录结构。在我们新建包的时候,一开始是没有composer.json这个文件的。

#### 在包中生成自己的composer.json

1. 执行 ```composer init```命令,，如下图所示:

  ![](/images/a.png)
  
  输入你想要生成的包名,回车:nangcheng/pfinal-array。如下图:

  ![](/images/b.png)
  
  输入你的包的描述(这里可以用英文装一个逼),回车:

  ![](/images/c.png)
  
  这里让你输入作者,我直接n  跳过,后期手工去填写自己牛逼的签名.
  然后一路回车到这里:

  ![](/images/d.png)

  上面这两条告诉你,有需要的依赖没有,我没有依赖所以就直接输入 n 回车了。

  然后接着回车,最后你发现你的文件夹中会生成一个composer.json 的文件了。

  打开composer.json 内容如下:

```
  {
    "name": "nancheng/pfinal-array",
    "authors": [
        {
            "name": "LXZ",
            "email": "Lampxiezi@163.com"
        }
    ],
    "require": {}
  }
```

#### 修改composer.json 文件

   1. authors 加入牛逼哄哄的 签名
   
     "authors": [  
        {
            "name": "南丞",
            "email": "Lampxiezi@163.com",
            "homepage": "http://friday-go.cc/"
        }
    ],
    
   2. 其他的如上面的composer.json 内容一样。

   3. 主要在autoload 上:

    "autoload": {                                                   //自动加载
        "psr-4": {                                                  //自动使用的 psr-4 格式
            "pf\\arr\\": "src/"
        }
    }

> 注意: 自动加载的时候 我们 用的是psr-4 至于什么是psr-4 那各位大爷就自行了解吧。pf\\arr\\ 这个 是我源码中的命名空间. src 是我源码的目录,代码如下:

```php
<?php
namespace pf\arr;
use pf\arr\build\Base;
class PFarr {
    protected $pf_array_link;
    protected function driver()
    {
        $this->pf_array_link = new Base();
        return $this;
    }
    public function __call($method, $params)
    {
        if (is_null($this->pf_array_link)) {
            $this->driver();
        }
        if (method_exists($this->pf_array_link, $method)) {
            return call_user_func_array([$this->pf_array_link, $method], $params);
        }
    }
    public static function single()
    {
        static $pf_array_link;
        if (is_null($pf_array_link)) {
            $pf_array_link = new static();
        }
        return $pf_array_link;
    }
    public static function __callStatic($name, $arguments)
    {
        return call_user_func_array([static::single(), $name], $arguments);
    }
}
```
修改完以后, 运行 ```composer update``` 命令 Ok;

------- 

### 发布

然后使用 git 把项目发布到 github上,git怎么用那就看 [git基本操作](http://friday-go.cc/Git-%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C)。

推送到github以后,上 composer包官网[链接 ](https://packagist.org/),使用github登录,
登录以后:

![](/images/f.png)

点击他会出来你发布的包列表:

![](/images/e.png)

点击上图中的show API Token 按钮 生成 所对应的Token,然后复制Token.

第二步:

   去github 网站你对用的项目中 操作如下图所示:

   ![](/images/g.png)

   在上图的搜索框中搜索 *Packagist* 然后点击编辑如下图所示:

   ![](/images/h.png)

   *token 填写你上面复制的Token*

第三步:
    
    如下图所示:
   ![](/images/m.png)
   
   ![](/images/n.png)

   点击submit（Check的使用会检查是否已经纯在这个目录了，如果存在的话，需要重新改一下composer.json文件中的name配置）

---------------------------------------

#### OK 到此 Composer包发布完毕

顺便放上我的包地址: [nancheng/pfinal-array](https://packagist.org/packages/nancheng/pfinal-array)
