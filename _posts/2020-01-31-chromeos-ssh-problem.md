---
layout: post
title: 解决 ChromeOS 系统 Secure Shell App 主机指纹不匹配问题
categories: chromebook
description: 解决 ChromeOS SSH 应用连接主机时 known_host SHA 指纹不匹配的问题
keywords: chromeos, secure shell
---

Pixelbook 是个很好的远程终端，配合官方的 Secure Shell App Chrome 应用程序远程 SSH 或是 SFTP 都很方便，比起从 Crostini 虚拟机连接少了一层 NAT。这篇是记录使用过程中的一个问题，方便以后找到。

起因是给家里的树莓派重装了个系统，升级到了 buster 版本，然而重装的系统必定 SHA 指纹不同，所以在使用 Pixelbook 远程连接的时候发生了冲突。  

```shell
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
d6:be:12:7e:22:23:c3:e1:56:30:d6:cd:65:b7:ab:42.
Please contact your system administrator.
Add correct host key in /.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /.ssh/known_hosts:7
ECDSA host key for xxxxxxxxxxxxx.yyy.au has changed and you have requested strict checking.
Host key verification failed.
NaCl plugin exited with status code 255.
(R)econnect, (C)hoose another connection, or E(x)it?
```
*这段是在网上找的，版本比较早，当时忘记截图了。*  
*这么做也是为了安全，以防中间人攻击*

这个问题在 Linux 中也会出现，解决方法一般是到 `.ssh/`目录下找到 known_hosts 文件删除相应主机的记录，或者可以直接修改记录。  
但是在这个 Chrome 应用中没办法直接修改主机记录，所以刚开始的时候还挺难办的。后来查了资料知道了解决办法。

Chrome 应用差不多也算是浏览器拓展，整个也是基于 Chrome 浏览器的,所以呼出控制台调用方法删除就好了。  
`CTRL+SHIFT+J`打开控制台，输入 `term_.command.removeKnownHostByIndex(INDEX)`, INDEX 就是上边提示的发生冲突的主机记录所在行。或者输入`term_.command.removeAllKnownHosts()`清空 known_hosts。
![console](https://chamboin.github.io/images/posts/chromebook/console.png)

谷歌已经宣布放弃 Chrome App，确实挺遗憾的，这个想法我个人认为挺好的，但是实现比较难，做的人也少，被砍掉也是无法避免的，且用且珍惜吧。

BTW，这个应用不只是在 ChromeOS 上可以用，PC 上也是可以的，到 Chrome 扩展商店搜索安装就可以了，地址栏输入 ssh 空格后跟主机。
![btw](https://chamboin.github.io/images/posts/chromebook/secureshellapp.png)