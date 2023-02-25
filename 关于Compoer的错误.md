最近博主在用composer的时候出现以下问题
安装用的是以下代码

    curl -sS https://getcomposer.org/installer | php
    mv composer.phar /usr/local/bin/composer

![错误截图1](https://img.mzrme.com/2020/05/24/c2060b917bef5.png)![错误截图2](https://img.mzrme.com/2020/05/24/39dbaaef633f0.png)
在经过半天的查找下找到了解决方案

    export COMPOSER_HOME="$HOME/.config/composer";
    composer

这个HOME就是你在shell输入`cd`所进入的目录，可以用以下命令选择

    HOME=/path

这里`path`就是你选择的目录，将其改成你要的就可以了
如果还有其他错误欢迎各位小伙伴在评论提出哦
To be continued #(高兴)
