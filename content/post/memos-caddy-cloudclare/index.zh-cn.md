+++
author = "Darkblue Soul"
title = "Memos+Caddy+Cloudflare自建微博"
date = "2024-11-19 15:08:59 +0800"
description = "在Racknerd VPS上docker搭建Memos，Caddy反向代理，Cloudflare R2图床"
categories = [
    "Web"
]
tags = [
    "memos", "caddy", "cloudflare", "R2", "https"
]
image = "memos.png"

+++

微博每次发了过几天就忍不住又改成自己可见，不知道是在担心什么。前段时间看到有介绍Memos自建微博的，略心动。这两天网上逛逛看到了Racknerd双11特惠VPS好便宜，没忍住。。。干脆$21.72买了两年。刚才想去找下链接，好像已经下架了。

最低配置如下：
|     |   |
|  ----  | ----  |
| RAM  | 1 GB RAM |
| CPU Cores  | 1 CPU Core |
| Operating System  | CentOS 7 64 Bit |

买的时候随便挑了个系统是CentOS7的，不大会用，为了用Docker安装Memos，直接reinstall了个Ubuntu 18.04 with Docker，这样就把装Docker这步省掉了，然后绑了个子域名[memos.darkbluesoul.cc]到VPS。

## Memos安装
[Memos文档Container Install - Memos](https://www.usememos.com/docs/install/container-install)

```
docker run -d \
  --init \
  --name memos \
  --publish 5230:5230 \
  --volume ~/.memos/:/var/opt/memos \
  neosmemo/memos:stable
```
装完直接`memos.darkbluesoul.cc:5230`就能访问了。

## caddy安装
本来Memos+Caddy按[Using HTTPS](https://www.usememos.com/docs/install/https)用Docker compose安装是最方便的，但是我前面安装compose都弄不好，干脆不用了。后来是按
[Install — Caddy Documentation](https://caddyserver.com/docs/install)安装的，下面代码我全部粘贴到XShell运行只运行了第一行，后来是一行一行粘进去运行的。
```
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
```
```
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
```
```
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
```
```
sudo apt update
```
```
sudo apt install caddy
```

## Caddy反向代理

修改/etc/caddy/Caddyfile，nano我用不习惯，还是用vi了。

```
vi /etc/caddy/Caddyfile
```

将Caddyfile中
```
:80 {
        # Set this path to your site's directory.
        root * /usr/share/caddy

        # Enable the static file server.
        file_server

        # Another common task is to set up a reverse proxy:
        # reverse_proxy localhost:8080

        # Or serve a PHP site through php-fpm:
        # php_fastcgi localhost:9000
}
```

改为
```
memos.your-domain.com {
        # Set this path to your site's directory.
        # root * /usr/share/caddy

        # Enable the static file server.
        file_server

        # Another common task is to set up a reverse proxy:
        reverse_proxy localhost:5230

        # Or serve a PHP site through php-fpm:
        # php_fastcgi localhost:9000
}
```

就用了caddy自带的TLS和反向代理到memos：5230端口

修改后`:wq`保存退出。

重启Caddy使配置生效：`systemctl reload caddy`

然后就可以直接通过`memos.darkbluesoul.cc`访问Memos了。
## Cloudflare R2 图床
Cloudflare的配置按[配置 Cloudflare R2 存储 - Memos](https://hahagood.com/post/2024/03/20240306212529-setup_cloudflare_r2_bucket_for_memos/)，还有[从零开始搭建你的免费图床系统（Cloudflare R2 + WebP Cloud + PicGo）](https://www.pseudoyu.com/zh/2024/06/30/free_image_hosting_system_using_r2_webp_cloud_and_picgo/)这篇非常详细。Cloudflare开R2的时候要信用卡犹豫了一下，不过想想这个应该就是我自己的操作会影响计划用量吧，应该还是可控的，开了个计划用量邮件提醒。

Memos配置按Memos文档[Configuring Cloudflare R2 Storage step-4](https://www.usememos.com/docs/advanced-settings/cloudflare-r2#step-4:-configure-memos)。

## Memos美化
用了[通过memos的自定义样式更改字体为霞鹜文楷](https://blog.memos.ee/archives/88.html)、[memos css美化分享](https://linux.do/t/topic/162835)、[memos自定义CSS：标签样式、字体大小、选项卡隐藏](https://blog.memos.ee/archives/190.html)

Memos——设置——系统——自定义样式里添加以下代码保存：
```
/* 字体美化 */
@import '//cdn.imsun.org/lxgw-wenkai-screen-webfont/lxgwwenkaigbscreen.css';body {font-family:"LXGW WenKai Screen",sans-serif;}

/* 设置 Memos 标签样式 */
span.inline-block.w-auto.text-blue-600.dark\:text-blue-400{
    color: #f3f3f3;
    background-color: #40b76b;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    border-radius: 2px;
    padding: 2px 6px;
    font-size: 15px;
    margin-bottom: 4px;
}

/* 设置同级下不同的标签显示不同的颜色 */
/* 第2个标签 */
span.inline-block.w-auto.text-blue-600.dark\:text-blue-400:nth-child(n+2) {
    background-color: #157cf5;
}
/* 第3个标签 */
span.inline-block.w-auto.text-blue-600.dark\:text-blue-400:nth-child(n+4) {
    background-color: #f298a6;
}
/* 第4个标签 */
span.inline-block.w-auto.text-blue-600.dark\:text-blue-400:nth-child(n+6) {
    background-color: #fdb15d;
}
/* 第5个标签 */
span.inline-block.w-auto.text-blue-600.dark\:text-blue-400:nth-child(n+8) {
    background-color: #67d6ca;
}
/* 第6个标签 */
span.inline-block.w-auto.text-blue-600.dark\:text-blue-400:nth-child(n+10) {
    background-color: #7445e0;
}
/* 设置 Memos 文本高亮样式 */
mark {
    background-color: #F27579;
    color: #f3f3f3;
    padding-left:2px;
    border-radius: 2px;
}

/* 修改Memo字号 */
.memo-wrapper .text-base {
    font-size: 0.95rem;
}

/* 修改代码块字号 */
.text-sm {
    font-size: 0.85rem;
}

/* 隐藏 通知 选项卡 */
#header-inbox {
  display: none;
}

/* 隐藏 个人资料 选项卡 */
#header-profile {
  display: none;
}

/* 隐藏 探索 选项卡 */
#header-explore {
  display: none;
}

/* 修改编辑器字体为等宽 */
textarea {
    font-family: 'Courier New', Courier, monospace;
}
```
## 后记
2024.11.21 Memos已删除。