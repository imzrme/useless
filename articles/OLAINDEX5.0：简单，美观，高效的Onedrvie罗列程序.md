# 准备
* 一台服务器，LAMP/LNMP/LANMP/LOMP（Mysql可选）
* 一个域名（有条件可以上两个）
* 一个可以Office账户（带Onedrive），最好为香港区的
# Office账号
[button href="https://a1.zxd.workers.dev"]免费A1香港区申请地址[/button]
[button href="https://freea1p.art"]免费A1P申请地址[/button]
[button href="https://freeoffice365.me/"]鸡妈的免费E3 SY[/button]
[button href="https://ethreesymsauto.laulzgoay.workers.dev/"]小俊的免费E3 SY[/button]
[button href="https://shop.v2ex.ltd/product/8.html"]E3 MSDN购买地址[/button]
# 搭建
## OLAINDEX
[tip type="warning" title="提示"]
罗列Onedrive的程序中，OLAINDEX不支持虚拟主机，若要使用虚拟主机，请使用[Oneindex](https://github.com/avedu/oneindex),这个应该是备份，原作者参与了维护
[/tip]
###安装方法

    cd web-project # 这里的'web-project'指的是网站目录
    git clone https://github.com/WangNingkai/OLAINDEX.git tmp 
    mv tmp/.git . 
    rm -rf tmp 
    git reset --hard 
    composer install -vvv  # 安装依赖项 
    chmod -R 777 storage # 重要！！！确保缓存目录具有读写权限 
    chown -R www:www * # 此'www'是指服务用户组 
    composer run install-app # 安装应用

**记得要把`/path to your website/storage`目录和目录下的内容给予`777`权限
然后就是绑定与配置步骤了。
###反代Ondrive下载提速
大佬的原文在这，讲了FODI和Oneindex的修改方法，[FODI反向代理 实现高速下载 OneDrive网盘文件](https://www.nbmao.com/archives/3916)
####Nginx反代配置
本文以宝塔面板为例子
1. **创建一个网站(可为静态)，配置好SSL（此步骤必须完成！！！！）**
2. 然后打开以下页面
![反向代理](https://img.mzrme.com/2020/07/28/eabe92f80bd7c.png)
3. 打开自己的Onedrive，随意下载一个文件，然后在下载列表中右键点击下载的文件，选择【复制链接地址】

    复制出的链接：
    https://m*****-my.sharepoint.com/personal/***_***_***/_layouts/15/download.aspx?SourceUrl=%2Fpersonal%2F***%5F***%5F***%2FDocuments%2FMOVIE%2F%E5%A4%A9%E6%B0%94%E4%B9%8B%E5%AD%90%2Emp4
    截选的部分：
    https://m*****-my.sharepoint.com

4. 把截选出来的部分填入步骤2的【目标url】中，提交即可

5. 打开后白屏的话等待一会儿，直到跳转到微软的登录界面即为成功

####FODI修改方法
打开`CloudFlare`的`Workers`的代码编辑界面

添加如下内容到`const ONEDRIVE_REFRESHTOKEN`的下面

    const ORIGIN_URL = "https://yours.sharepoint.com"    //填入上面截选出的链接
    const PROXY_URL = "https://yours.domainname.com"    //填入你创建的网站的链接

然后翻到代码最下面，找到两个`return JSON.stringify`，在最下面的那个的最后添加代码（注意要有前面的点）

    .replace(RegExp(ORIGIN_URL,"g"),PROXY_URL)

最后保存
然后直接打开 workers.dev 链接
此时下载已经经过你的服务器代理了@(滑稽)
####Oneindex修改方法
打开`oneindex目录/lib/onedrive.php`
找到

    foreach((array)$data['value'] as $item){
    
    //var_dump($item); $items[$item['name']] = array( 'name'=>$item['name'], 'size'=>$item['size'], 'lastModifiedDateTime'=>strtotime($item['lastModifiedDateTime']), 

    'downloadUrl'=>$item['@microsoft.graph.downloadUrl'], 'folder'=>empty($item['folder'])?false:true ); }

并修改`'downloadUrl'=>$item['@microsoft.graph.downloadUrl'],`为

    'downloadUrl'=>str_ireplace("截取的链接","反代的链接",$item['@microsoft.graph.downloadUrl']),

####Nginx特殊配置
大文件下载他会预先下载部分内容，对于小硬盘来说会直接撑爆硬盘，还有预载的超快下载速度会使CPU很容易占用超过50%（针对某mach机器和其他限制严重机器）还要再限制一下缓存占用

    #↓↓↓↓↓以下是小缓存设置↓↓↓↓
    proxy_buffering on;
    proxy_buffer_size 4k;
    proxy_buffers 8 2M;
    proxy_busy_buffers_size 10M;
    proxy_max_temp_file_size 0;
    #↓↓↓↓↓↓↓↓以下是0缓存设置↓↓↓
    proxy_buffering off;

添加到Nginx配置中的很多排的`proxy_set_header`的下面即可！

**至于`OLAINDEX`的话，之前有小伙伴提了issue，但是作者还没回答，建议大家发个邮件或再提一个issue问问