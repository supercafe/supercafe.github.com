---
layout: post
tags : [Jekyll]
title: Jekyll中无法使用comments功能
---

11月初首次接触Jekyll+gibhub的方式建立自己的博客系统，后来开始在这上面写日志，日益觉得少了这功能少了那功能，于是开始慢慢地完善它。

最近两天都在纠结这个评论功能。按照官方文档介绍，要添加评论功能，首先得需要一个第三方的评论系统，可以是`diqus`，`duoshuo`等。然后在`_config.yml`中进行一些配置：

```
comments :
  provider : disqus
  disqus :
    short_name : jekyllbootstrap
  livefyre :
    site_id : 123
  intensedebate :
    account : 123abc
  facebook :
    appid : 123
    num_posts: 5
    width: 580
    colorscheme: light
```
 我在disqus上是注册了一个用户名的，在`_config.yml`中添加这些配置无效，`short_name`改成我注册的用户名。后来慢慢摸索，参考了别人的网站，fork了他们的分支，发现少了点东西，就是`JB`文件夹，因为这个是需要`Jekyll bootstrap`支持，所以拷贝了文件夹。
 
 然后看见`_layouts`文件夹中`default.html`,`post.html`,中引用的是_includes/theme下的`default.html`,`post.html`，打开他们后发现在post.html中引用了评论功能：

 ```
 </div>
	<div class="clearboth"></div>
	{% include JB/comments %}
	<div class="clearboth"></div>
</div>
```
这里实际上就是触发评论脚本：定义在`JB/comment`中的：

```
{% if site.JB.comments.provider and page.comments != false %}

{% case site.JB.comments.provider %}
{% when "disqus" %}
  {% include JB/comments-providers/disqus %}
{% when "livefyre" %}
  {% include JB/comments-providers/livefyre %}
{% when "intensedebate" %}
  {% include JB/comments-providers/intensedebate %}
{% when "facebook" %}
  {% include JB/comments-providers/facebook %}
{% when "duoshuo" %}
  {% include JB/comments-providers/duoshuo %}
{% when "custom" %}
  {% include custom/comments %}
{% endcase %}

{% endif %}
```

对于多说的provider，会引用多说的js：

<!-- 多说评论框 start -->
<div id="comments">
    <div class="ds-thread" {% if page.id %}data-thread-key="{{ page.id }}"{% endif %}  data-title="{% if page.title %}{{ page.title }} - {% endif %}{{ site.title }}"></div>
</div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"{{ site.JB.comments.duoshuo.short_name }}"};
	(function() {
		var ds = document.createElement('script');
		ds.type = 'text/javascript';ds.async = true;
		ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
		ds.charset = 'UTF-8';
		(document.getElementsByTagName('head')[0] 
		 || document.getElementsByTagName('body')[0]).appendChild(ds);
	})();
	</script>
<!-- 多说公共JS代码 end -->

这些都是通用的，拷贝过来直接用就可以。

此外在_includes/theme/Snail/defalut.html中还开启了：

` {% include JB/analytics %}`

并且也在_config.yml中配置了这一项：

```
analytics :
    provider : google 
    google : 
        tracking_id : 'UA-123-12'
    getclicky :
      site_id : 
    mixpanel :
        token : '_MIXPANEL_TOKEN_'
    piwik :
        baseURL : 'myserver.tld/piwik' # Piwik installation address (without protocol)
        idsite : '1'                   # the id of the site on Piwik
 ```
 所有的这些配置都有了，但是还是无法弹出评论框。不知道是什么原因，这里先记录下。待问题解决了之后再更新解决方案。

 