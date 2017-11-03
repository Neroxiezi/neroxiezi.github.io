---
layout: default
---
<div class="jumbotron" style="padding-left:50px;position: relative;">
  <h1 style="float: left;width:100%;font-size:80px;text-align:left">Hello, world!</h1>
  <p style="float: left;">一个有信念者所开发出的力量，大于99个只有兴趣者。</p>
	<div style="clear: both;"></div>
</div>
<div class="container">
	<div class="row">
		<div class="col-md-8">
			<div class="panel panel-default" style="text-align: left">
			  <div class="panel-heading"><span class="glyphicon glyphicon-zoom-in"></span> 文章列表</div>
			  <div class="panel-body">
			      <div data-am-widget="list_news" class="am-list-news am-list-news-default" >
				  <!--列表标题-->
						  <div class="am-list-news-bd">
							  <ul class="am-list">
							      {% for post in site.posts %}
								      <li class="am-g am-list-item-desced">
								          <a href="{{ post.url }}" class="am-list-item-hd "><h2>{{ post.title }}</h2></a>
								          <div class="am-list-item-text">
								          	{{post.desc}}
								          </div>
								          <br>
								          <div style="font-size:14px;"><span class="glyphicon glyphicon-user"></span> {{post.author}} <span class="glyphicon glyphicon-time"></span> {{post.date| date:"%Y-%m-%d %H:%M"}} </div>
								      </li>
					                {% endfor %}
							  </ul>
						  </div>
				    </div>
			  </div>
			</div>
		</div>
		<div class="col-md-4">
		<div class="panel panel-default" style="text-align: left">
		  <div class="panel-heading"><span class="glyphicon glyphicon-download"></span> 分类列表</div>
		  <div class="panel-body">
		    <div class="list-group">
			  <a href="#" class="list-group-item disabled">
			     首页
			  </a>
			  <li class="list-group-item">
				  <a href="https://github.com/Neroxiezi" class="am-icon-btn am-icon-github"></a>
				  <a href="##" class="am-icon-btn am-icon-twitter"></a>
				  <a href="##" class="am-icon-btn am-icon-facebook"></a>
			  </li>
			  <a href="#" class="list-group-item disabled">友情链接</a>
			  <li class="list-group-item">
			    {% include link.html %}
			  </li>
			</div>
		  </div>
		</div>
		</div>
	</div>
</div>