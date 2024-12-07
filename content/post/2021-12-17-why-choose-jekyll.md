---
title:  jekyll之折腾
date:   2021-12-17 13:32:20 -0600
description: 从jekyll到gridea，再hugo，再回到jekyll.
tags: [jekyll, gridea, hugo, MXlinux, Linux Mint]
categories: [Jekyll]
mathjax: yes
toc: yes
key: 2021-12-17-why-choose-jekyll
---
## jekyll对新手略难
Linux Mint安装[jekyll](http://jekyllcn.com/)，有点忘了，装ruby就折腾了好久，各种出错，应该就是Linux命令不熟的原因，最后算是弄好了，git push都正常，就是觉得主题不好看，下个主题么，又是缺东少西的，就懒得弄了。后面无聊搜了下Linux发行版的排名，下了个排第一的MXLinux，重装系统，jekyll啥的就都没了。
<!--more-->
## Gridea简单，模板少
懒得再装jekyll，试了下grdiea，很方便，可惜模板太少了。像我这种连CSS都懒得改的，那就最好是拿来就能用的。然后看网上说什么现在jekyll都转hugo了，就又想着用hugo。
## hugo很方便
尤其是我选的[acdemic](https://themes.gohugo.io/themes/hugo-academic/)主题，[wowchemy](https://wowchemy.com/zh/)系列，支持netlify一键部署到github，而且repo名随意，建好了自定义个netlify二级域名就可以访问了。
其实我建了两个，另一个是hogo-blog什么的，就是几个喜欢的主题都是头像在最上面的，不喜欢，还一点就是要本地生成public再部署，也不喜欢。
舍弃wowchemy，干脆自己部署，主题找了个even，还不错，就是hugo serve后本地访问没问题，push到github就无法访问，最后才想起自己部署错了，把整个站点push上去了，当然没法访问。
## 再用回jekyll就觉得简单了
无聊看了下jekyll的主题，就第一个[Adam Blog ver2.0](http://jekyllthemes.org/themes/adam-blog-2/)，手机访问很好看啊，又开始折腾jekyll。
MXlinux安装ruby：
`sudo apt-get install ruby`
结果出错，说要单独安装dev，网上查了下，应该是要装完整包，再用
`sudo apt-get install ruby-full`
就解决了。
`gem install jekyll`
安装到最后说没权限，要求密码，输入root用户密码就可以了。后面再装就直接
`sudo gem install jekyll`
就没有这个问题了。
jekyll主题是下的zip，解压以后修改config，再
`bundle install`
再git全套就好了，
`git push -u origin master`出错，被拒绝，搜了下用
`git push -f`
解决了。
现在犯愁的是SVG没弄过，这几天还要再下个AI做一个。
总之，先这样吧，域名也绑了，这两天在纠结要不要阿里云买个便宜点的域名做个备案，只是便宜的后缀都好长，三位的两个我前面都买了又不想用在博客上。算了，贵点就贵点吧，看今年能不能坚持写下去吧。

2022.05.03 换**hugo**了。
