---
title: Ubuntu 20.04+Caddy+PHP+MySQL搭建Typecho
# description: 'Racknerd VPS Ubuntu 20.04用Caddy、MySQL、PHP搭建Typecho.'
published: 2024-12-24

tags: [Caddy, PHP, MySQL]
category: Code
---




最近弄Hugo弄的有点烦了，知道github page不会被百度收录后，又部署到vercel、Cloudflare、netlify，又弄评论，waline、twikoo、giscus、cusdis都试过了，主题又不喜欢了，各种挑主题，然后就有那么一刻突然啥都不想弄了。

装个Typecho吧，wordpress以前用了很多年，就Typecho没用过。这个服务器一直没用，反正便宜，折合人民币一年70好像是，那就装个呗。


<!--more-->


主要参考[在 Ubuntu 22.04 上轻松安装 Caddy 和 PHP 8.1](https://cn.linux-console.net/?p=34595)以及[在Ubuntu 22.04 LTS 上安装 MySQL两种方式：在线方式和离线方式](https://blog.csdn.net/weixin_45626288/article/details/133220238)，现在用的服务器1C1G，不敢装22.4所以用了个20.04。

还算顺利，中间还看了其他文章，有些不适用的我自己乱套出错就不提了。如果跟我一样用20.04的注意下PHP版本，我是7.4的，文章里面是8.1，改Caddyfile时要修改下fpm版本号。还一点，改完设置后，一定要记得重启服务。

Caddy重启：
```shell
systemctl reload caddy
```
[常见的Caddyfile模式](https://caddyserver.com.cn/docs/caddyfile/patterns#frankenphp)提到个[frankenphp](https://frankenphp.dev/docs/)比使用PHP-FPM快4倍，又搞的我很心动，不知道要求服务器什么配置，算了，以后再说吧。

---
2024.12.27 已经删了，用回hugo了，部署在vercel。

