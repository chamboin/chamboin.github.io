---
layout: post
title: 使用Docker搭建Caddy+Typecho个人博客网站
categories: Docker
description: 个人博客站容器化
keywords: Docker, Caddy, Typecho
---

近些年各种社交平台发展的很好，现在已经不流行写博客了，加上知乎简书一类的平台，折腾个人博客的就更少了。但是对于我来说还是觉得搭建个人博客站还是挺好的，因为一些技术性的东西并不适合也不想去分享到社交平台上，搭建一个web就是有了自己的一个小天地，可以记录下来学到的技术性的东西，方便自己查找，对于需要的人也以提供一些帮助，最主要的还是自己比较喜欢折腾吧。  
好了废话说完了，开始正题

---

**目录**

* TOC
{:toc}

## 各service简介

什么是Docker相信肯定不用我在这里罗哩罗嗦介绍了，Docker的logo很形象地展示了它的思路--一只鲸鱼驼着好多集装箱。  
有了Docker你的各种服务都打包成了一只只集装箱，相互隔离，也不需要在主机上安装各种依赖配置各种环境，也不需要考虑各种复杂的权限问题，对于开发也是非常友好的。至于性能问题这就不是我需要考虑的了，我还没有那种需要考虑宿主机和容器性能差异的应用，实在不行不是还有那句老话嘛：“世界加钱可及”。  

### Docker的安装

使用的第一步肯定是安装啦，Docker的安装这里先不赘述了，包括国内主机更换镜像源等操作在另一篇中进行详细介绍[Docker安装以及更换国内镜像源][1]。  
关于Docker的使用请参考[官方文档](https://docs.docker.com)。

### Docker-Compose

Docker-compose是一个容器编排工具，可以一次性管理多个容器。对于有服务依赖，比如webserver和php还有数据库往往需要同时启动，这时候用命令行去一个个敲`docker run`显然不够优雅，而且万一手滑输错某个参数还需要删除容器重新开始。使用docker-compose之后只需要编写好compose文件，执行`docker-compose up`就可以启动所有服务，特别方便。  
当然，这个工具也是需要安装的，同样这里不讲安装，安装请参考[Docker安装以及更换国内镜像源][1]。

### Caddy

caddy是一个新兴的web服务器，使用Go语言编写，对比与老牌的nginx、apache等优势在于配置简单，自动支持TLS等。但是参考文档相比起来比较少，出了问题也很有可能找不到答案。

这里我是因为之前没有使用nginx的经验，没有什么历史包袱，所以当然选择更新更简单的caddy作为Web服务器了，配置文件对比nginx少几十行，何乐而不为呢。  
这里就使用官方的镜像运行容器就可以了，对于简单的应用官方镜像足够了，也省的初次使用docker的同志们再去学习怎么build镜像，临时有功能需要也可以进入容器手动安装，以后的需求可以以后再学习。

### PHP

其实php在这里不是特别必要，因为caddy的官方镜像中包括了php，可以直接使用。考虑Docker的思想，一个容器运行一个服务，把它分开也挺好。同样使用官方镜像，我看到好多教程要么自己build镜像要么使用别人编译的镜像，对于入门来说门槛还是高了点。

### Mysql

数据库没什么特别的，喜欢用什么用什么，甚至你还可以加上redis做缓存都是可以的，只要在PHP的容器中做好相应的数据库驱动就好了。这里还是使用官方的镜像。

## 部署开始！

推荐使用docker-compose部署，毕竟这么多的容器，命令行启动实在是麻烦，不过别担心，使用命令行单独启动我也会介绍。

下面给出我的docker-compose.yml文件
```yml
version: '3'

services:

    caddy:
        image: abiosoft/caddy
        ports:
            - "80:80"
            - "443:443"
        volumes:
            - ./path/to/your/Caddyfile:/etc/Caddyfile
            - /path/to/certificate/.caddy:/root/.caddy
            - /your/site/root:/srv
        restart: always
        environment:
            - CLOUDFLARE_EMAIL=your_email_here
            - CLOUDFLARE_API_KEY=your_api_key
            - ACME_AGREE=true
            - TZ=Asia/Shanghai
        networks:
            - frontend

    db:
        image: mysql:5.7
        volumes:
            - ./path/to/database:/var/lib/mysql
        restart: always
        expose:
            - "3306"
        environment:
            - MYSQL_ROOT_PASSWORD=password
            - MYSQL_DATABASE=typecho
            - MYSQL_USER=typecho
            - MYSQL_PASSWORD=password
            - TZ=Asia/Shanghai
        networks:
            - frontend

    php:
        image: php:fpm
        volumes:
            - /your/site/root:/var/www/html
        restart: always
        expose:
            - "9000"
        environment:
            - TZ=Asia/Shanghai
        depends_on:
            - db
            - caddy
        networks:
            - frontend

networks:
    frontend:
```
下面大至介绍下各部分的作用，详细的`docker-compose.yml`文件的语法请参考官方reference：<https://docs.docker.com/compose/compose-file/>。

- 首行的`version: '3'`是必要的，表明使用的compose版本，注意**版本1和2语法不能兼容**。
- `services`是要启动的服务，是一级项目，其中的内容需要缩进。
- `caddy`等二级项目是服务名称，可以通过`docker-compose start/stop/restart service_name`单独控制某一服务。
- `image`是使用镜像名称，因为我是用的都是官方镜像，不需要`build`条目，启动时会检查本地镜像文件，本地不存在时会自动拉取镜像，无需提前拉取。
- `volumes`绑定目录，进行持久化存储。这个选项将宿主机的目录映射到容器中，是必需内容，否则容器删除后数据也就都没有了。还有各服务的配置文件也需要挂载以便容器启动时读取。将上面我的实例文件中的路径换成你要存储数据的路径即可，其中`./`表示当前目录，即docker-compose.yml文件所在目录。你所绑定的目录需提前创建(我不太确定会不会自动创建)。
- `restart`是重启策略，可以不写，具体参考官方reference。
- `environment`是环境变量设置。
- `exose`开放指定端口。
- `depends_on`是version 3才有的参数，其中指定的服务会先于当前服务启动。
- `network`指定容器连接的虚拟网络，连接在同一网络的服务可以使用服务名进行通信。version 3不推荐使用`--link`，使用`network`替代其功能，也更方便管理。
- 一级的network是虚拟网络的定义，可以指定网络类型和参数等，我是用了默认的网络类型，参数部分留空就可以。
啊对了，TZ设定时区完全是出于我的强迫症，不指定的话应该是使用UTC，无所谓的。

### Caddy配置

caddy的官方镜像是`abiosoft/caddy`，使用`abiosoft/caddy:php`镜像可以在caddy容器中同时运行php，不需要单独运行php镜像。官方在[docker hub](https://hub.docker.com/r/abiosoft/caddy)和[github](https://github.com/abiosoft/caddy-docker)上都有文档，我只说一下需要用到的内容。  
容器中默认的配置文件路径是`/etc/Caddyfile`，所以需要映射本机的配置文件存储路径；默认证书文件存储在`/root/.caddy`文件夹中，也需要映射；网站根目录是`/srv`对应nginx中的`/var/www/html`，需要映射，并且php镜像需要映射相同的网站根目录。  
`ports`将容器内的端口绑定到宿主机的端口，因为是web，所以绑定监听宿主机的80和443端口，也就是http协议和https协议的默认端口。  
我还使用了cloudflare作为CDN，所以在环境中填写了相关配置，使用cloudflare的TLS证书而不是自动申请Let's Encrypt证书。我的IP之前进过黑名单，所以套一个CDN就算IP被墙还可以继续访问，如果你没有需求可以不用，毕竟对于好多情况来说CDN是减速器2333。ACME_AGREE这个是caddy官方推荐设置的，这条需要写上。

贴出我的Caddyfile以供参考
```Caddyfile
yourdomain.com, sub.domain.com
{
    gzip
    browse
    tls {
        dns cloudflare
    }
    //不使用CDN时可以直接tls指令后跟你注册域名使用的邮箱，自动申请证书
    rewrite {
        if {path} not_has admin
        to {path} {path}/ /index.php
    }
    //这里rewrite规则是配合为静态化设置，非必须
    fastcgi / php:9000 php {
        root /var/www/html
    }
    log ./caddy.log
}
```
唠叨一句，Caddyfile里fastcgi设置，花括号前的php记得要写上，这个是preset参数，忘记写的话可能会出现php执行错误。root设置是因为php和caddy不在同一容器中，要指定php的site root。

### 数据库配置

数据库没特殊需求就Mysql呗，使用官方镜像，这里我指定了版本，因为记得之前搭wordpress的时候用了latest的镜像踩了个坑，也不记得解决方案了，干脆还是用老版本的。  
直接在环境中设置好root密码，创建好网站需要使用的数据库、用户、密码，开放默认的端口，这个端口只开放给创建的虚拟网络并不会暴露在公网当中，所以使用默认的3306就好。

### php配置

php容器主要做一下网站根目录映射，开放默认端口就可以，注意一点就是官方镜像没有安装Mysql的驱动，我的解决办法是服务up之后，输入`docker exec -it php_contianer_ID/name bash`，进入php容器内部，执行`docker-php-ext-install pdo_mysql`。这只是暂时的办法，彻底的解决办法就是写Dockerfile基于官方的镜像创建带有mysql扩展的镜像。

如果使用caddy自带的php可以省去这部分。

### Typecho

差点忘了主角了，到官网[下载Typecho][2]，上传到想要挂载的网站根目录(wget更方便嘛)，解压，把解压出来的build目录中所有文件放到网站根目录，删除空的build目录就可以了，剩下的就是在浏览器中完成安装了。建议使用**开发版**，正式版有BUG可能会导致无法完成安装。

一切正常的话打开浏览器输入你的域名可以看到  
![startscreen.png](/images/posts/docker/installscreen.png)
开始配置，输入相应的数据库配置，这里要选择Mysql而不是Sqlite。输入你设置的用户和密码，数据库地址要填你的数据库服务名称，端口根据自己配置填。  
![configscreen.png](/images/posts/docker/configscreen.png)
至于我为什么使用Typecho，因为它支持markdown写文章，比wordpress更轻量化，虽然hexo这种静态的也可以，但是又不想完全和我的[Github pages][3]内容相同，所以现在就先用它了。以后可能也还会用wordpress吧，毕竟功能强大主题和插件又多，完全不会前段的我又不会自己做主题插件什么的。

### 运行

把所有需要的文件(Caddyfile，v2ray的config.json等)都准备好之后只需要在你的docker-compose.yml所在的目录下执行`docker-compose up -d`就完成了。  
想要停止并删除容器只需要`docker-compose down`就可以了。  
修改了某一服务的配置文件只需要`docker-compose restart service_name`重启相应的服务。

关于docker-compose的高级应用我也正在学习当中，比如集群部署什么的，以后学会了会~~鸽~~写的。

## 如果你不想使用docker-compose

不想用docker-compose那么可以手动执行启动容器。运行：  
`docker run [options] image`  
其中option有
```shell
-d   //后台运行
-e   //等同environment
-p   //端口绑定
-v   //存储挂载，相当于volumes
--link   //连接到某容器
--name   //设定容器名称，不可重复
```
-e和-p和-v参数可以多次使用指定多个，例如：
```shell
docker run -d \
--name v2ray \
-v /etc/v2ray:/etc/v2ray \
-p 10000:10000 \
-p 123:123 \
-e TZ=Asia/Shanghai \
v2ray/official
```
其他容器启动同理，写好配置参数就可以了。

服务如果不正常可以使用`docker logs [container]`查看日志，找Google问原因。

## Over

整个流程大致就是这样了，要说哪里有问题的话那就是权限配置了。docker官方是不推荐以root用户运行的，可以新建普通用户加入到docker组，不过VPS这种一般都是自己使用，所以我就只用root用户。我知道这是不对的，我也不推荐你这么做，但是我懒啊，一般情况下也不会出什么岔子。以后再系统地学习一下Linux，把权限管理什么的都做好，现在嘛，又不是不能用(滑稽)

还有就是关于网络安全的，包括黑客攻击什么的，没有配置像样的安全策略。不过我这种小破站，有什么好攻击的嘛，不值得。

广告一下，欢迎访问[我的小站](https://www.chamboin.tk)，会有和Github Pages不一样的内容哦。

Enjoy!


[1]:https://chamboin.github.io/2019/11/25/docker-install/
[2]:http://typecho.org/download
[3]:https://chamboin.github.io