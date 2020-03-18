## 前言
&#160; &#160;&#160; &#160; 前两天在神州 Z7M-CT7GS上装了macOS-Catalina(黑苹果)，在使用中发现，mac用来开发简直不要太爽！！！！但是！！因为学校开课有网站建设，需要php集成环境，之前Windows用的phpStydy，而mac上找了一圈没有发现好用的php集成环境，然后通过百度发现了Valet和Docker这两个东西，一开始装的是Valet，因为足够轻量，但是在安装DedeCMS的过程中发现了一些问题，无论访问织梦的哪个页面都只能显示主页，百度很久也没有发现解决方法，最后还是选择了Docker。最后研究学习了两天多，整出来了这个lnmp整合包。

## Docker简介

&#160; &#160;&#160; &#160; Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

## 相关网址

- [Docker官方文档](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Docker教程(菜鸟教程)](https://www.runoob.com/docker/docker-tutorial.html)

## 项目地址
- [DockerHub：zeroy233/lnmp](https://hub.docker.com/repository/docker/zeroy233/lnmp)
- [GitHub：ZeroY-Code/Docker-LNMP](https://github.com/ZeroY-Code/Docker-LNMP/)

## 使用方法

```shell
docker pull zeroy233/lnmp:1.0  # 从DockerHub拉取LNMP镜像
git clone https://github.com/ZeroY-Code/Docker-LNMP.git # 从GitHub拉取项目
cd Docker-LNMP  # 进入到项目目录
docker-compose up -d lnmpServices  # 运行项目
```



## 文件详解

```
Docker-LNMP
  │
  ├── docker-compose.yml  # docker配置文件
  ├── mysql  # mysql相关文件 
  │			└── data  # 数据库文件 (docker无法持久存储，故将整个数据库拉了出来)
  ├── nginx # nginx相关文件
  │			├── vhost # 其他站点相关配置
  │			│			└── debug.conf  # 默认站点相关配置
  │			│
  │			└── nginx.conf # nginx主配置文件
  ├── php # php 相关文件
  │		 └── php.ini # php文件
  │
  ├── www  # 网站目录(非站点目录)
  │		 └── debug # 默认站点目录
  └── wwwlogs # 网站log相关目录
  
```

## 如何新建网站？

1. 在nginx目录下的vhost下新建一个 “站点.conf” 文件 可复制默认debug.conf内容到里面 更改相关目录与端口即可
2. 在www建立关联上述配置文件站点地址的目录即可

例如：

新建一个test站点

```
# ./nginx/vhost/test.conf
server {
    listen 82;  # 更改端口为82
    #listen [::]:80 default_server ipv6only=on;
    server_name _;
    index index.html index.htm index.php;
    root  /home/wwwroot/test; # 设置网站目录为test(这里的test必须包含在Docker-LNMP/www/下，/home/wwwroot/为统一目录，不可更改)
    #error_page   404   /404.html;
    # Deny access to PHP files in specific directory
    #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }
    include enable-php.conf;
    location /nginx_status {
        stub_status on;
        access_log   off;
    }
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        expires      30d;
    }
    location ~ .*\.(js|css)?$ {
        expires      12h;
    }
    location ~ /.well-known {
        allow all;
    }
    location ~ /\. {
        deny all;
    }
    access_log  /home/wwwlogs/access.log;
}
```

```php
# ./www/test/index.php

<?php echo phpinfo(); ?>
```

即可
