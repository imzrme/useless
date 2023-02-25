#前言
说起开源论坛程序，我们都会想到国内两大巨头：discuz和phpwind。一个拥抱腾讯一个拥抱阿里，实力确实不容小视。当然他们本身确实也是做得很强大，不仅仅是论坛，还可以用来做CMS，企业网站等等。大家都知道虎嗅网一开始也是用的discuz！（还有宝塔面板）

所以，程序强大了，也就使得程序本身变得越来越臃肿，对于那些只想单纯做一个论坛的用户来说，很显然这些臃肿的程序已经不适合他们了。于是我们开始寻找国内外的一些其他替代品，轻论坛产品。

国内目前的一些轻论坛产品，像xiunobbs，startbbs等等，体验下来，总感觉差点意思。
#Flarum
上个月，一次偶让的机会在网上看到国外一个新兴的开源社区程序——Flarum。
Flarum是一款现代的，优雅的，简洁的，强大的论坛软件。Flarum 让在线交流变得更加轻松愉快。虽然现在他只是Beta版（已更新到beta12），但是相信在未来一定会退出正式版本！（听说开发者走了一个）
不过这个开发者的理由有点扯淡
[Why We're Building Flarum](https://flarum.org/story/)
Flarum官网并不支持中文，国内衍生了不少Flarum中文网，这里推荐两个主要的
1.[Flarum 中文站:优雅简洁的轻论坛](https://www.flarum.org.cn)  这个主要是讲官方的代码，主要基于官方的教程翻译而来
2.[FlarumChina](https://www.flarumchina.org)  这个网站主要是基于官方代码进行本土化，二次打包，进行发布，对于新手支持较好
这里我个人比较推荐Flarumchina，他的网站和软件都是同步官方更新的，挺不错。
他们的论坛[TowerLight Community](https://flarum.atowerlight.cn)
#安装教程
虚拟机安装可以看 [gitee releases](https://gitee.com/FlarumChina/FlarumChina/releases/v0.1.0-beta.12) 或者 [github releases](https://github.com/skywalker512/FlarumChina/releases/tag/v0.1.0-beta.12)，下载压缩包

下面是内置 nginx 和 fpm 的 docker 镜像，据维护者说,在稳定下来之后将会有更多选项,目前代码在 [点击前往](https://gitee.com/FlarumChina/flarum) 中

那么目前最便捷的方法就是docker [镜像地址](registry.cn-hangzhou.aliyuncs.com/flarumchina/nginx-flarum:beta12-test)

端口号：8888

以下是环境变量参考

    DEBUG=false
    FORUM_URL=http://xxx
    
    # FlarumChina 特殊的可以使用 cdn 来加速静态资源，若不使用，请与 FORUM_URL 相同
    FORUM_CDN = xx 
    
    DB_HOST=xx
    DB_NAME=xx
    DB_USER=xx
    DB_PASS=xx
    DB_PREF=xx
    DB_PORT=3306
    
    FLARUM_ADMIN_USER=admin
    FLARUM_ADMIN_PASS=xxx
    FLARUM_ADMIN_MAIL=xxx@xxx.com
    FLARUM_TITLE=xxx
##宝塔面板安装
###安装面板
请访问[面板信息](https://bt.cn/download/linux.html)以获取安装信息。
据博主发文前，最新版为v0.1.0-beta.12[点击前往下载](https://github.com/skywalker512/FlarumChina/releases/download/v0.1.0-beta.12/beta12.zip)
###安装环境
![安装环境](https://img.mzrme.com/2020/04/13/2f08d4d70f03e.png)
在这里面Nginx，PHP，Mysql是必须的，请不要忘记。
###安装PHP插件
![安装PHP插件](https://img.mzrme.com/2020/04/13/2ba1a8511ba60.png)
![安装PHP插件](https://img.mzrme.com/2020/04/13/198cdd738e5aa.png)
###网站配置伪静态
![网站配置伪静态](https://img.mzrme.com/2020/04/13/4543f9d11457d.png)
如果懒的话可以用这个凑合一下
    location / {
      try_files $uri $uri/ /index.php?$query_string;
    }

<!--more-->

当然最好还是全部写入
    location / {
      try_files $uri $uri/ /index.php?$query_string;
    }
    
    location = /sitemap.xml { 
      try_files $uri $uri/ /index.php?$query_string; 
    }
    
    location ~* \.(?:manifest|appcache|html?|xml|json)$ {
      add_header Cache-Control "max-age=0";
    }
    
    location ~* \.(?:rss|atom)$ {
      add_header Cache-Control "max-age=3600";
    }
    
    location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|mp4|ogg|ogv|webm|htc)$ {
      add_header Cache-Control "max-age=2592000";
      access_log off;
    }
    
    location ~* \.(?:css|js)$ {
      add_header Cache-Control "max-age=31536000";
      access_log off;
    }
    
    location ~* \.(?:ttf|ttc|otf|eot|woff|woff2)$ {
      add_header Cache-Control "max-age=2592000";
      access_log off;
    }
    
    gzip on;
    gzip_comp_level 5;
    gzip_min_length 256;
    gzip_proxied any;
    gzip_vary on;
    gzip_types
        application/atom+xml
        application/javascript
        application/json
        application/ld+json
        application/manifest+json
        application/rss+xml
        application/vnd.geo+json
        application/vnd.ms-fontobject
        application/x-font-ttf
        application/x-web-app-manifest+json
        application/xhtml+xml
        application/xml
        font/opentype
        image/bmp
        image/svg+xml
        image/x-icon
        text/cache-manifest
        text/css
        text/plain
        text/vcard
        text/vnd.rim.location.xloc
        text/vtt
        text/x-component
        text/x-cross-domain-policy;
###设置运行目录
要确保网站根目录结构如下
* Apache 请开启 mod_rewrite 并将网站根目录设置到 /path/to/flarum/public
* Nginx 进行如下设置 并将网站根目录设置到 /path/to/flarum/public
这里博主展示Nginx的配置方法（宝塔面板内通用）
![设置运行目录](https://img.mzrme.com/2020/04/13/8500bd57f009b.png)
设置运行目录为 public
![设置运行目录](https://img.mzrme.com/2020/04/13/46d93a2dc5fd4.png)

接下来访问网站，进行设置，就可以了#(傻笑)<br>

#####No related posts.

