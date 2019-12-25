# Pixelbook 本地化

关于 Linux 的本地化，网上搜一搜好多人不建议 Liunx 使用中文作为系统默认语言，认为可能会出现不兼容或乱码等问题。其实我觉得完全没有必要担心，Linux 发展至今关于中文的本地化做的已经比较完善了，没有必要坚持使用英文，该用中文就用中文好了。目前我的所有运行 Linux 的机器都用的中文，并没有出现任何问题。

## 修改时区

本地化当然要用北京时间啦，特别是服务器用 UTC 时区或者别的时区，看系统日志或者错误信息的时候会很麻烦，用北京时间也不会影响各种服务也能方便我们运维。  
以 Debian 为例，终端运行`dpkg-reconfigure tzdata`然后选择想要的时区设定就可以了，很简单的。其他发行版自己找找吧，Ubuntu 应该也可以用这个。  
关于修改相应文件达到目的的我不推荐啦，不够优雅，有工具可以用的就不要去直接改文件。

## 更改系统默认语言

### 安装语言包

一般情况下都会各发行版都会内置中文语言包，应该不需要手动安装，以防万一这里还是再说一下吧。

1. 修改 **locale.gen** 文件
   >sudo vim /etc/locale.gen
   
   去掉 `zh_CN.GBK GBK`,  `zh_CN.UTF8 UTF8` 的注释，或直接添加这两行内容。

2. 运行 `sudo locale-gen` 下载语言。

### 修改默认语言编码

> sudo dpkg-reconfigure locales

选择系统的默认语言，推荐选择 `zh_CN.UTF8 UTF8` 编码问题真的很让人头疼，UTF8 解千愁。  
如果没改过来重启虚拟机试试。

## 修复中文乱码

中文显示乱码问题是缺少字体导致的，安装中文字体可以解决。

### 安装中文字体

> sudo apt install xfonts-wqy ttf-wqy-zenhei ttf-wqy-microhei

同样的，有问题请重启。

## 安装中文输入法

都使用中文环境了，当然要装中文输入法了，经典的小企鹅装一下吧（Debian 应该都自带了）。
> sudo apt install fcitx

### 配置 Fcitx

> im-config

点击确定->点击Yes->选择fcitx->确定->确定

重启使配置生效

### 安装谷歌拼音输入法

> sudo apt install fcitx-googlepinyin

### 添加输入法

> fcitx-config-gtk3

在界面中添加谷歌拼音输入法
