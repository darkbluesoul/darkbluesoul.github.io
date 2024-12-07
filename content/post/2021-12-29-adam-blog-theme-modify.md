---
title:  Adam Blog 2.0主题修改
date:   2021-12-29 09:42:20 +0800
description: 删除mailchimp，修改Disqus为来必力，修改中文字数统计，修改摘要显示.
tags: [mailchimp, disqus, livere, excerpt, number]
categories: [Jekyll]
mathjax: yes
toc: yes 
key: 2021-12-29-adam-blog-theme-modify
---

## 删除mailchimp

mailchimp原来的账号想不起来了，暂时懒得弄，不知道用邮件订阅的多不多？
_config.yml里面mailchimp注释掉好像也有显示的，有点忘了，不喜欢，就直接把newsletter.html整个注释掉了，就不显示了。

<!--more-->

## 修改Disqus为来必力

国内访问disqus实在太慢了，改成[来必力](http://livere.com/)好像还行，不过也不是很快,先用着吧。
打开_includes/disqus.html，找到

```html
{% if site.discus_identifier %}
```

这个是判断config有没有填写disqus帐号的，有就显示评论，没有就不显示，我把这条删了;

找到
```html
<div id="disqus_thread"></div>
```
在中间加入你的来必力代码，加好以后就下面这样：

```html
<div id="disqus_thread">
	<!-- 来必力City版安装代码,隐藏广告id -->
	<style>
		#taboola-livere { display: none;}
	</style>
	<div id="lv-container" data-id="city" data-uid="M*****************==">
		<script type="text/javascript">
			(function(d, s) {
				var j, e = d.getElementsByTagName(s)[0];
			
				if (typeof LivereTower === 'function') { return; }
			
				j = d.createElement(s);
				j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
				j.async = true;
			
				e.parentNode.insertBefore(j, e);
			})(document, 'script');
		</script>
		<noscript> 为正常使用来必力评论功能请激活JavaScript</noscript>
	</div>
	<!-- City版安装代码已完成 -->
</div>
``` 


删除原disqus代码：

```javascript
<script>
            (function() {
                var d = document, s = d.createElement('script');
                s.src = '//{{ site.discus_identifier }}.disqus.com/embed.js';
                s.setAttribute('data-timestamp', +new Date());
                (d.head || d.body).appendChild(s);
            })();
          </script>
      <noscript>Please enable JavaScript to view the comments.</noscript>
```

保存。

## 修改中文字数统计

这个模板英文统计没问题，中文么就不行了，参照[ The filter number_of_words can't parse Chinese correctly #1921 ](https://github.com/jekyll/jekyll/issues/1921)

打开_includes/post-meta.html，找到


```html
{% assign words = document.content | strip_html | number_of_words %}
```


修改为

```html
{% assign words = document.content | strip_html | size %}
```


就可以正常统计中文字数了。阅读时间就没改了，好像有篇文章说英文跟中文阅读速度差不多的，恩，主要是懒。

## 修改摘要显示

模板本来是按100个单词截取摘要的，本来以为我中文字数改了也会正常了，结果存档页还是整篇显示，搜了一下，[Jekyll 使用记录](https://cyhour.com/189/)这里找到办法了，原来[官方文档](http://jekyllrb.com/docs/posts/#post-excerpts)就有写的。

在_config.yml里面添加一行
```yml
excerpt_separator: '<!--more-->'
```

然后打开_pages/archive.html，找到

```html
{{ post.content | markdownify | strip_newlines | truncatewords: 100 }}
```

改为

```html
{{ post.excerpt }}
```



这样设置有点烦的地方就是每篇文章里都要在摘要后面加上`<!--more-->`，多了自定义的快乐，少了自动的简便。不过没事，复制粘贴一下也不会很辛苦。其实这里我弄了好久，还以为自己代码弄错了，结果一个呢是config里面定义分割符的时候少了引号，还一个最坑的就是我一直在改_layouts/post.html，all post页面其实是archive.html。

唉，思维惯势不可取，还一点是要细心，其实目录结构里看一下就知道了，就是每次翻倒都是扫一眼就过，该。

PS:发布的时候一直出错，显示'if' tag was never closed，按[GitHub 博客-- Jekyll--代码高亮，Liquid 转义字符](https://blog.csdn.net/zhangpeterx/article/details/103920538)的方法加了raw终于解决了，还一个就是tag里我写了个size。。。两个问题我花了好几个小时才解决。
